# 📖 Use Case Specifications (RoDevsy)

> **Document Status:** Draft v1.0
> **Scope:** Core Platform Workflow (Auth, Marketplace, Escrow, Finance)

---

## 🔐 Module A: Identity & Onboarding

### UC-001: User Registration with Roblox Verification
- **Use Case ID:** UC-001
- **Primary Actor:** Unregistered User (Potential Developer or Client)
- **Goal:** Create a secure account linked to a verifiable Roblox identity.
- **Preconditions:**
  1. User has a valid email address.
  2. User owns an active Roblox account.
- **Trigger:** User clicks "Sign Up" on the landing page.

#### Main Success Scenario (Basic Flow)
1. **Actor** enters email, password, and agrees to TOS.
2. **System** validates password strength and email uniqueness.
3. **System** creates a temporary account record (Status: *Unverified*).
4. **System** sends a verification code to the Actor's email.
5. **Actor** enters the OTP code.
6. **System** verifies email and prompts for "Roblox Identity Verification."
7. **Actor** clicks "Connect with Roblox."
8. **System** redirects Actor to Roblox OAuth authorization page .
9. **Actor** grants permission to RoDevsy.
10. **System** retrieves Roblox User ID, Avatar, and Account Age.
11. **System** creates the final User Profile and logs the Actor in.

#### Extensions (Alternate Flows)
- **2a. Email already exists:**
  - System displays error: "Account exists. Try logging in."
  - Use case ends.
- **10a. Roblox Account is too new (Anti-Alt Protection):**
  - System detects Roblox account age < 30 days.
  - System denies registration: "Roblox account must be >30 days old to prevent spam."
  - Use case ends.

#### Exceptions (Error Flows)
- **8a. Roblox OAuth Service Down:**
  - System displays: "Roblox Verification unavailable. Please try again later."
  - System saves state so user can resume verification later.

---

## 💰 Module B: Financial Operations

### UC-002: Deposit Funds (Crypto to Credits)
- **Use Case ID:** UC-002
- **Primary Actor:** Client
- **Goal:** Convert external cryptocurrency (USDT) into internal RoDevsy Credits.
- **Preconditions:**
  1. Actor is logged in and "Active".
  2. Actor has an external crypto wallet with funds.
- **Trigger:** Actor navigates to Wallet > "Deposit Funds".

#### Main Success Scenario (Basic Flow)
1. **Actor** selects deposit currency (e.g., USDT-TRC20).
2. **System** generates a unique, one-time wallet address linked to this transaction.
3. **System** displays QR code and address string with a 30-minute timer.
4. **Actor** initiates transfer from their external wallet.
5. **System** (Background Worker) detects the transaction on the blockchain.
6. **System** waits for 3 network confirmations.
7. **System** updates Actor's "Available Balance" (1 USDT = 1 Credit).
8. **System** sends "Deposit Confirmed" email notification.

#### Extensions (Alternate Flows)
- **6a. Insufficient confirmations:**
  - System marks transaction as "Pending" until safe threshold is reached.
- **4a. User sends unsupported currency (e.g., BTC to USDT address):**
  - System cannot recover funds (Blockchain limitation).
  - System displays warning disclaimer beforehand to mitigate this liability.

#### Exceptions (Error Flows)
- **2a. Crypto Node API Timeout:**
  - System fails to generate address.
  - System displays: "Deposit service temporarily unavailable."

---

## 🤝 Module C: Marketplace & Escrow

### UC-003: Post a Project (Job Listing)
- **Use Case ID:** UC-003
- **Primary Actor:** Client
- **Goal:** Publish a job listing to attract qualified developers.
- **Preconditions:**
  1. Actor is logged in.
  2. Actor has sufficient *Available Balance* OR intends to deposit later (System allows posting but blocks hiring without funds).
- **Trigger:** Actor clicks "Post a Project".

#### Main Success Scenario (Basic Flow)
1. **Actor** fills in "Title", "Description", and uploads "Reference Files".
2. **Actor** selects "Assigned For" tags (e.g., *Builder, Scripter*) 

[Image of project posting workflow diagram]
.
3. **Actor** defines Budget Range (e.g., 500 - 1000 Credits) and Deadline.
4. **System** validates inputs (Profanity filter, Budget limits).
5. **Actor** clicks "Publish".
6. **System** sets status to "Active" and indexes the job for search.
7. **System** notifies Developers with matching Skill Tags.

#### Extensions (Alternate Flows)
- **4a. Sensitive content detected:**
  - System flags project as "Pending Review."
  - System alerts Moderators.
  - Use case pauses until Moderator approval.

---

### UC-004: Hire Developer & Lock Escrow
- **Use Case ID:** UC-004
- **Primary Actor:** Client
- **Secondary Actor:** Developer (Passive recipient)
- **Goal:** Select a candidate and secure funds to start the contract.
- **Preconditions:**
  1. Project is "Active".
  2. At least one Proposal has been received.
  3. Client has sufficient *Available Balance* >= Proposal Bid Amount.
- **Trigger:** Client clicks "Accept Proposal" on a specific candidate.

#### Main Success Scenario (Basic Flow)
1. **Actor** reviews proposal and clicks "Hire".
2. **System** displays "Escrow Confirmation" modal showing Total Cost + Fees.
3. **Actor** confirms "Lock Funds".
4. **System** performs ACID transaction:
   - Debit *Client Available Balance*.
   - Credit *Escrow/Frozen Balance*.
5. **System** changes Project Status to "In Progress".
6. **System** notifies Developer: "You are hired! Funds are secured."

#### Exceptions (Error Flows)
- **4a. Insufficient Funds:**
  - System detects Balance < Bid Amount.
  - System redirects Actor to **UC-002 (Deposit Funds)**.
  - Transaction is aborted.

---

### UC-005: Approve Work & Release Funds
- **Use Case ID:** UC-005
- **Primary Actor:** Client
- **Secondary Actor:** Developer
- **Goal:** Complete the contract and transfer earnings to the Developer.
- **Preconditions:**
  1. Project is "In Progress".
  2. Developer has marked work as "Submitted".
- **Trigger:** Client reviews deliverables and clicks "Approve".

#### Main Success Scenario (Basic Flow)
1. **Actor** downloads/reviews the submitted files/links.
2. **Actor** clicks "Release Funds".
3. **System** prompts for final rating (1-5 Stars) and review text.
4. **Actor** submits review.
5. **System** performs irreversible transfer:
   - Debit *Escrow Balance*.
   - Deduct *Platform Fee* (Revenue).
   - Credit *Developer Available Balance*.
6. **System** updates Developer's "Skill Tier" progress (FR-002).
7. **System** marks project "Completed".

#### Extensions (Alternate Flows)
- **1a. Work is unsatisfactory:**
  - Actor clicks "Request Revision".
  - System notifies Developer with feedback.
  - Status reverts to "In Progress".
- **1b. Dispute required:**
  - Actor clicks "Open Dispute" (Triggers UC-006 - Dispute Resolution).

---

## 🛡️ Module D: Administration

### UC-006: Resolve Dispute
- **Use Case ID:** UC-006
- **Primary Actor:** Admin / Moderator
- **Secondary Actors:** Client, Developer
- **Goal:** Manually arbitrate a stuck contract and force fund allocation.
- **Preconditions:**
  1. A Dispute ticket exists for a specific Project ID.
- **Trigger:** Admin opens the "Dispute Dashboard".

#### Main Success Scenario (Basic Flow)
1. **Actor** reviews chat logs, file submissions, and project requirements.
2. **Actor** communicates with both parties via "Dispute Chat".
3. **Actor** determines the verdict (e.g., 50/50 split, or Full Refund).
4. **Actor** executes "Force Resolution":
   - System unlocks *Escrow Balance*.
   - System distributes funds according to verdict.
5. **System** closes the project and dispute ticket.
6. **System** logs the decision in the Audit Trail.

