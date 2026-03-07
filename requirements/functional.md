# Functional Requirements

## 1. Authentication & Identity Management
* **FR-001 (Registration):** Mandate email verification and Roblox OAuth 2.0 linkage to assign "Active" status; purge unverified accounts after 48 hours.
* **FR-002 (Access Control):** Enforce Argon2 hashing for credentials and secure JWT session handling. 2FA is mandatory for logins from novel devices or IPs.
* **FR-003 (Password Recovery):** Implement time-sensitive, one-time use reset links (30-min expiry) with generic responses to mitigate account enumeration attacks.
* **FR-004 (Session Lifecycle):** Terminate sessions immediately upon logout or timeout; clear local client-side storage and server-side tokens.



[Image of user authentication flow chart]


## 2. User Profile & Skill Progression
* **FR-005 (Skill Initialization):** Users must select primary roles (e.g., Scripter, Builder) during onboarding, initializing them at Tier 0 (Novice).
* **FR-006 (Tier Progression):** Increment experience counters upon successful project completion; system must automatically promote users based on defined job counts.
* **FR-007 (Champion Status):** Grant "Champion" status to users with 95%+ Job Success Scores and 5-star ratings over their last 10 jobs; trigger visual "Glow" effects and priority placement in directory searches.
* **FR-008 (Portfolio Management):** Users may upload work samples or link `roblox.com` Place IDs. Links must be validated for domain authenticity and tagged by skill category to support Tier validation.

## 3. In-Platform Credit System (Wallet)
* **FR-009 (Deposits):** Generate unique, one-time crypto wallet addresses for USDT deposits. Credit internal balance at a 1:1 ratio after 3 network confirmations.
* **FR-010 (Escrow Lock):** Atomically move funds from "Available Balance" to "Escrow/Frozen Balance" upon contract initialization to guarantee payment security.
* **FR-011 (Withdrawals):** Validate withdrawal requests against available balance and minimum limits (10 Credits). Deduct fees and queue for batch processing.

## 4. Project Marketplace & Posting
* **FR-012 (Listing Creation):** Enforce strict input validation for Title, Description, Budget, and Deadline. Link every job to a specific Skill Tag (as per FR-006) to ensure talent matching.
* **FR-013 (Moderation):** Automated filter for profanity/sensitive keywords. Flagged projects must be held in "Pending Review" status by Admins.
* **FR-014 (Lifecycle Management):** Maintain version history for edits. Prevent "Assigned For" tag or Budget modifications once a developer is hired to ensure contract stability.



## 5. Bidding & Proposal System
* **FR-015 (Proposal Submission):** Allow Developers to submit bids and cover letters. System must highlight if the developer's current Skill Tier matches the job requirements.
* **FR-016 (Hiring Flow):** Upon selection, the Client must confirm an Escrow Deposit. Automatically trigger FR-010 and notify rejected applicants.

## 6. Contract & Milestone Execution
* **FR-017 (Work Submission):** Developers submit deliverables; trigger "Review Pending" status and start a 7-day auto-approval timer.
* **FR-018 (Fund Release):** Upon client approval, transfer frozen credits to Developer's available balance (less platform fee) and trigger FR-006 to increment experience.
* **FR-019 (Dispute Resolution):** The "Open Dispute" trigger freezes the auto-approval timer and creates a private, admin-mediated channel for the Client, Developer, and Admin.

## 7. Game Asset Marketplace
* **FR-020 (Asset Listing):** Require minimum screenshots/video and asset file (.rbxm, .rbxl). All assets must pass automated virus/backdoor scans to protect community integrity.
* **FR-021 (Purchase Flow):** Verify buyer balance and perform an instant transfer of credits to the Seller. Generate a secure, one-time download link for the Buyer upon success.