# 📦 Product Backlog & Roadmap: RoDevsy

> **Developer:** Pir Ahmed Shah (Solo)
> **Timeline:** 60 Days (12 Iterations of 5 Days each)
> **Target Launch:** Late January 2026
> **Pace:** Sustainable / Solo-Dev Friendly

## 1. 🗓️ Strategic Roadmap (The "Big Picture")

| Phase | Cycle | Iterations | Focus Area | Goal |
| :--- | :--- | :--- | :--- | :--- |
| **I** | **Foundation** | 1 - 3 | Identity & Database | Users can Login and have a secure profile. |
| **II** | **The Bank** | 4 - 6 | Financial Ledger | Users can Deposit USDT and see Credits. |
| **III** | **The Product** | 7 - 9 | Escrow & Contracts | Users can create jobs and hire devs. |
| **IV** | **Polish** | 10 - 12 | UI & Disputes | The app looks good and handles errors safely. |

---

## 2. 🏃 Phase I: Foundation (Days 1-15)
*Goal: Get the "boring" stuff (Auth, Legal, DB) right so you never have to touch it again.*

### Iteration 1: Repo Setup & CI/CD (Completed)
- [x] Initialize Repos (`api`, `web`, `docs`).
- [x] Setup Linting & Prettier.

### Iteration 2: Identity (Active)
- [ ] **[API]** Implement **Roblox OAuth Strategy**.
    - *Task:* Exchange code for `access_token` and get `sub` (Roblox ID).
- [ ] **[WEB]** Build **Login Page** (Center Card, "Connect Roblox" Button).
- [ ] **[WEB]** Create **Auth Context** (Store User Session in React).

### Iteration 3: Compliance & Profiles
- [ ] **[WEB]** **Terms of Service Modal**.
    - *Task:* Create modal that forces scroll-to-bottom before "Accept" enables.
- [ ] **[API]** **User Schema Update**.
    - *Task:* Add `isVerified`, `agreedToToS` (timestamp), `role` fields.
- [ ] **[API]** **Sanctions Check (Mock)**.
    - *Task:* Create middleware that checks IP geo (allow all for now, but have the function ready).

---

## 3. 🏦 Phase II: The Bank (Days 16-30)
*Goal: Build the internal economy. No UI focus, just pure logic.*

### Iteration 4: The Ledger (Schema)
- [ ] **[API]** Create **Wallet & Transaction Tables**.
    - *Constraint:* `CHECK (balance >= 0)`.
    - *Fields:* `id`, `userId`, `balance` (BigInt), `currency` ('CREDIT').
- [ ] **[API]** Write **"Money Movement" Service**.
    - *Task:* Write a function `transferFunds(from, to, amount)` that runs in a SQL Transaction.

### Iteration 5: Deposits (Crypto)
- [ ] **[API]** **USDT Webhook Listener**.
    - *Task:* Create endpoint `/webhooks/deposit` that validates a mock Blockchain signature.
- [ ] **[API]** **Idempotency Logic**.
    - *Task:* Ensure the webhook doesn't credit the user twice if sent twice.

### Iteration 6: Withdrawal Requests
- [ ] **[WEB]** **Wallet Dashboard UI**.
    - *Task:* Simple card showing "Current Balance: 0 Credits".
- [ ] **[API]** **Withdrawal Endpoint**.
    - *Task:* Input address -> Deduct Balance -> Create "PENDING" Transaction record.

---

## 4. 🤝 Phase III: The Product (Days 31-45)
*Goal: The actual Escrow functionality.*

### Iteration 7: Contract Creation
- [ ] **[API]** **Contract Schema**.
    - *States:* `DRAFT` -> `PENDING_ACCEPT` -> `ACTIVE` -> `COMPLETED`.
- [ ] **[WEB]** **"Create Job" Form**.
    - *Fields:* Title, Description, Price, Deadline.
- [ ] **[API]** **Locking Logic**.
    - *Task:* When Client clicks "Create", atomically move funds from `Wallet` to `EscrowVault`.

### Iteration 8: The Workspace (Chat & Files)
- [ ] **[WEB]** **Contract Details Page**.
    - *Task:* View status, see description.
- [ ] **[API]** **File Upload Endpoint (S3)**.
    - *Task:* Allow Developer to upload `.rbxm` files.
    - *Security:* Only allow download if User is the Client AND Status is `PAID` (or `PREVIEW`).

### Iteration 9: Workflow Logic
- [ ] **[API]** **"Submit for Review" Action**.
    - *Task:* Dev clicks button -> Status becomes `IN_REVIEW`.
- [ ] **[API]** **"Release Funds" Action**.
    - *Task:* Client clicks button -> Funds move from `EscrowVault` to `DevWallet`.

---

## 5. ✨ Phase IV: Polish & Launch (Days 46-60)
*Goal: Make it look professional and handle edge cases.*

### Iteration 10: Disputes & Admin
- [ ] **[WEB]** **"Open Dispute" Button**.
    - *Logic:* Only visible if status is `ACTIVE` or `IN_REVIEW`.
- [ ] **[API]** **Admin Override Endpoint**.
    - *Task:* Allow Admin (You) to force-complete or force-cancel a contract via Postman/API.

### Iteration 11: The Marketplace (Asset Store) - *Lite Version*
- [ ] **[WEB]** **Asset Grid Component**.
    - *Task:* Display a list of "Featured Assets" (hardcoded/seeded initially).
- [ ] **[WEB]** **Buy Button**.
    - *Task:* Reuse the `transferFunds` logic for instant purchase.

### Iteration 12: Final Testing & Deployment
- [ ] **[OPS]** **Deploy to Production**.
    - *Task:* Push DB to Supabase/Neon, App to Vercel/Railway.
- [ ] **[QA]** **"The $1 Test"**.
    - *Task:* Use real USDT to test the full flow (Deposit -> Hire -> Release -> Withdraw).

---

## 6. 🧊 Icebox (Post-Launch)
*Things to worry about in February 2026.*
* Real-time Chat (Websockets) - *Use simple page refresh or polling for V1.*
* Email Notifications (SendGrid).
* Mobile App.
* Complex Analytics Graphs.

