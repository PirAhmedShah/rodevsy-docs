# ⚙️ Functional Requirements (RoDevsy)

## FR-001: User Authentication & Identity Management
**Description:** Secure user lifecycle management including OIDC/OAuth, session handling, and security enforcement.
**Priority:** High



[Image of user authentication flow chart]


### Scenarios

**1. User Registration & Onboarding**
> **GIVEN** a new user is on the registration page
> **WHEN** they submit the registration form
> **THEN:**
> * Validate that the password meets complexity requirements (min 12 chars, 1 special, 1 number).
> * Validate that the email format is correct and not currently in use.
> * Sanitize all text inputs to prevent XSS injection attacks.
> * Create the account in an "Unverified" state.
> * Trigger a verification email to the user's inbox.
> * Redirect the user to a "Verify Email" prompt.
> * After email verification, prompt the user to verify their Roblox account via OAuth.
> * Upon successful Roblox verification, change account status to "Active."
> * Upon unsuccessful verification, provide options to resend the verification email.
> * Display detailed inline error messages if validation fails.
> * Delete unverified accounts automatically within 48 hours.
> * Log the registration event in the security audit log.
> * Send a welcome email upon successful account activation.

**2. Secure Login & Access Control**
> **GIVEN** a user attempts to log in via standard or OAuth methods
> **WHEN** credentials are submitted
> **THEN:**
> * Check credentials against the hashed password (Argon2) in the database.
> * Support Roblox OAuth 2.0 to verify developer identity.
> * Lock the account temporarily (2 hours) after 5 consecutive failed attempts.
> * Grant access to the dashboard upon success.
> * Issue a secure, HTTP-only session Json Web Token (JWT).
> * Detect if the login device/IP is novel; if so, trigger the 2FA challenge flow.
> * Update the "Last Login" timestamp in the user profile.
> * Maintain a list of active sessions for the user, allowing remote logout.

**3. Password Recovery & Security**
> **GIVEN** a user cannot access their account
> **WHEN** they initiate the "Forgot Password" flow
> **THEN:**
> * Verify the email exists without revealing user existence to potential attackers (Generic response).
> * Send a time-sensitive, one-time use reset link (expires in 30 mins).
> * Invalidate all active sessions upon successful password reset.

**4. Session Lifecycle**
> **GIVEN** an active authenticated session
> **WHEN** a termination event occurs (Logout action or Timeout)
> **THEN:**
> * Invalidate the authentication token immediately on the server side.
> * Clear local storage/cookies on the client side.
> * Redirect the user to the public landing page.

---

## FR-002: User Profile and Skill Progression
**Description:** System to establish trust via Roblox verification, portfolios, and a tiered skill progression system based on completed work.
**Priority:** High

### Scenarios

**1. Skill Selection & Initialization**
> **GIVEN** a user is completing their profile setup
> **WHEN** they select their primary roles (UI Designer, Scripter, Builder, Game Designer, Animator)
> **THEN:**
> * Allow selection of multiple roles.
> * Initialize all selected roles at **Tier 0 (Novice)**.
> * Display these roles on the public profile as "Interested In" or "Learning."

**2. Tier Progression (Experience Based)**
> **GIVEN** a developer completes a project marked with a specific "Assigned For" tag (e.g., "Builder")
> **WHEN** the project status changes to "Completed"
> **THEN:**
> * Increment the experience counter for that specific skill track.
> * **Upgrade to Tier 1 (Apprentice):** Upon completing 3 successful jobs in that category.
> * **Upgrade to Tier 2 (Proficient):** Upon completing 10 successful jobs in that category.
> * **Upgrade to Tier 3 (Expert):** Upon completing 25 successful jobs in that category.
> * Notify the user of their rank up via dashboard animation and email.
> * Update the profile badge to reflect the new Tier level.

**3. Champion Status (Success Based)**
> **GIVEN** a user is already at Tier 3 for a specific skill
> **WHEN** they maintain a 95%+ Job Success Score and receive 5-star ratings on their last 10 consecutive jobs
> **THEN:**
> * Award the **Champion** rank for that skill (e.g., "Champion Scripter").
> * Apply a visual "Gold/Glow" effect to their profile card in search results.
> * Grant priority placement in the "Recommended Developers" algorithm.
> * Revoke Champion status if the Success Score drops below 90%.

**4. Portfolio Management**
> **GIVEN** a user identifying as a Developer
> **WHEN** they edit their portfolio section
> **THEN:**
> * Allow upload of past work thumbnails/videos (limit 50MB).
> * Allow linking to live Roblox Place IDs.
> * Validate links to ensure they point to the `roblox.com` domain.
> * Require users to tag portfolio items with specific skills (e.g., "This image represents Building") to support the Tier validation process.

---

## FR-003: In-Platform Credit System (Wallet)
**Description:** Internal ledger system for managing virtual currency (Credits) to facilitate instant escrow without direct fiat/crypto handling per transaction.
**Priority:** High

### Scenarios

**1. Depositing Funds (Crypto/USDT)**
> **GIVEN** a Client wants to fund their account
> **WHEN** they initiate a deposit request
> **THEN:**
> * Generate a unique, one-time crypto wallet address (e.g., USDT TRC20) for the user.
> * Monitor the blockchain for incoming transactions to that address.
> * Credit the user's internal wallet at a 1:1 ratio (1 Credit = 1 USDT) after 3 network confirmations.
> * Send an email notification confirming funds availability.
> * Log the transaction with a hash reference for audit trails.

**2. Freezing Funds (Escrow Lock)**
> **GIVEN** a user accepts a project proposal
> **WHEN** the contract is initialized
> **THEN:**
> * Check user balance for sufficient funds covering the Agreed Budget + Platform Fee.
> * Deduct the total amount from "Available Balance" and move it to "Escrow/Frozen Balance".
> * Prevent withdrawal of these funds while the project is active.
> * Notify the Developer that funds are secured in Escrow.

**3. Withdrawal**
> **GIVEN** a Developer has available credits
> **WHEN** they request a withdrawal
> **THEN:**
> * Validate the withdrawal amount is <= "Available Balance."
> * Enforce a minimum withdrawal limit (e.g., 10 Credits).
> * Deduct platform withdrawal fees (gas fees + service charge).
> * Place the request in a "Processing" queue for admin approval or automated batch processing.

---

## FR-004: Project Marketplace & Posting
**Description:** End-to-end workflow for drafting, validating, moderating, and publishing project listings.
**Priority:** High



[Image of project posting workflow diagram]


### Scenarios

**1. Project Composition & Role Tagging**
> **GIVEN** a client is accessing the "Post a Project" interface
> **WHEN** they interact with the form inputs
> **THEN:**
> * Must have fields for Title, Description, Budget Range, Deadline, and File Attachments.
> * Require the client to select "Assigned For" tags (Builder, Scripter, UI Designer, etc.) which links the job to the Skill System (FR-002).
> * Validate that Title is between 10-100 characters.
> * Validate that Description is between 50-2000 characters.
> * Validate Minimum Budget is > 1 Credit.
> * Validate Maximum Budget is < 10,000 Credits.
> * Auto-save progress to local storage every 30 seconds.
> * Validate that the "Deadline" is at least 24 hours in the future.

**2. Submission & Moderation**
> **GIVEN** a client has filled out valid project details
> **WHEN** they click "Publish Project"
> **THEN:**
> * Run text content through a profanity/sensitive keyword filter.
> * If flagged, hold in "Pending Review" status and notify moderators.
> * If clean, set status to "Active" and index in the search engine immediately.
> * Generate a unique, SEO-friendly URL slug (e.g., `/project/1023-roblox-map-builder`).
> * Send a confirmation email with project details and link.
> * Display the project in the client's "My Projects" dashboard.

**3. Lifecycle Management**
> **GIVEN** a client is managing an existing project
> **WHEN** they attempt to edit or close
> **THEN:**
> * Version the project history (store snapshot of previous version).
> * Notify removed applicants if critical details (Budget/Scope) change.
> * Prevent editing of "Assigned For" tags or Budget if a developer has already been hired.
> * Allow "Soft Delete" which removes it from search but keeps it in history.

---

## FR-005: Bidding & Proposal System
**Description:** Mechanism for developers to apply for jobs and for clients to select the best candidate.
**Priority:** High

### Scenarios

**1. Submitting a Proposal**
> **GIVEN** a Developer views an "Active" project
> **WHEN** they submit a proposal
> **THEN:**
> * Highlight if the Developer's Skill Tier matches the project's "Assigned For" tag (e.g., "You are a Tier 3 Scripter, this job needs a Scripter").
> * Validate the Bid Amount is within the Client's specified range.
> * Require a "Cover Letter" explaining why they fit the role.
> * Allow attachment of specific portfolio items relevant to the job.

**2. Client Selection**
> **GIVEN** a Client views received proposals
> **WHEN** they accept a specific proposal
> **THEN:**
> * Prompt the Client to confirm the "Escrow Deposit".
> * Trigger the **FR-003 (Freezing Funds)** workflow.
> * Change Project Status from "Active" to "In Progress."
> * Notify rejected applicants that the position has been filled.

---

## FR-006: Contract & Milestone Execution
**Description:** The active phase of work where deliverables are exchanged and funds are released.
**Priority:** Critical

### Scenarios

**1. Work Submission**
> **GIVEN** a project is "In Progress"
> **WHEN** the Developer submits a deliverable (file or link)
> **THEN:**
> * Change status to "Review Pending."
> * Start a "Auto-Approval Timer" (e.g., 7 days).
> * Notify the Client via email and dashboard notification.

**2. Approval & Fund Release**
> **GIVEN** the Client reviews the work
> **WHEN** they click "Approve & Release Funds"
> **THEN:**
> * Irreversibly transfer the escrowed Credits from the "Frozen" state to the Developer's "Available Balance."
> * Deduct the platform service fee (e.g., 5%).
> * Mark the contract as "Completed."
> * **Trigger:** Send signal to FR-002 to increment the Skill Tier counter for the specific "Assigned For" role.

**3. Dispute Initiation**
> **GIVEN** a disagreement arises
> **WHEN** a user clicks "Open Dispute"
> **THEN:**
> * Freeze the auto-approval timer immediately.
> * Notify the counterparty and RoDevsy Admins.
> * Open a dedicated "Dispute Chat Room" accessible only to Client, Developer, and Admin.

---

## FR-007: Game Asset Marketplace
**Description:** A store-front for buying/selling pre-made Roblox assets (models, scripts, maps).
**Priority:** Medium

### Scenarios

**1. Listing an Asset**
> **GIVEN** a Developer wants to sell a pre-made asset
> **WHEN** they upload the asset file (.rbxm, .rbxl)
> **THEN:**
> * Scan the file for malicious scripts (Backdoors/Viruses).
> * Require a minimum of 3 screenshots or a preview video.
> * Set a fixed Credit price (Minimum 1 Credit).
> * Apply a watermark to preview images automatically.

**2. Purchasing an Asset**
> **GIVEN** a User views an asset listing
> **WHEN** they click "Buy Now"
> **THEN:**
> * Verify the user has sufficient "Available Balance."
> * Instantly transfer credits from Buyer to Seller (minus platform fee).
> * Generate a secure, temporary download link for the Buyer.
> * Add the asset to the Buyer's "My Library" section.