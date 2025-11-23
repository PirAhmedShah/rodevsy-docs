# 🗺️ User Journey Map: RoDevsy Platform

> **Version:** 1.0
> **Status:** Draft
> **Source Context:** Personas, Use Cases, Functional Requirements

## 1. Executive Summary
This document outlines the emotional and functional paths for the two core personas: **Alex (The Developer)** and **Sarah (The Client)**. It highlights the transition from "High Anxiety" (fear of scams on Discord) to "High Confidence" (via RoDevsy's Escrow).

---

## 2. Journey A: The Developer ("Alex")
**Persona:** Independent Roblox Developer (16-25 yrs).
**Goal:** Monetize scripting skills without fear of non-payment.
**Scenario:** completing a "Weapon System" commission.

| Stage | User Actions | System Touchpoints | Thinking | Emotional State | Opportunities |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Onboarding** | • Lands on RoDevsy.<br>• Signs up via Email.<br>• Links Roblox Account. | • **UC-001:** Validates Email.<br>• **OAuth:** Fetches Roblox Avatar & ID. | *"Is this legit? I hope they don't steal my account."* | 😟 Skeptical | Display "Verified by Roblox" badge prominently to build immediate trust. |
| **2. Job Acceptance** | • Receives offer for "Weapon Script".<br>• Reviews requirements.<br>• Accepts contract. | • **Smart Contract:** Initializes state.<br>• **Notification:** "Client Funds Secured in Escrow". | *"Okay, the money is actually there. They can't run away."* | 😌 Relieved | Send a push notification: "Funds Locked: 500 Credits." |
| **3. Execution** | • Develops script in Roblox Studio.<br>• Uploads `.rbxm` file to Workspace.<br>• Marks "Ready for Review". | • **File Scan:** Checks for viruses/backdoors.<br>• **Status:** Updates to *In Review*. | *"I hope they don't invent fake issues to cancel."* | 😐 Focused | Auto-generate a preview/watermarked version if possible. |
| **4. Payment** | • Receives approval notification.<br>• Sees balance move to "Available".<br>• Requests Crypto Withdrawal. | • **Ledger:** Credits Wallet.<br>• **Skill Tier:** Points ++. | *"Nice! Money in the bank. And my rating went up."* | 🤩 Delighted | Prompt Alex to share his generic "Portfolio Link" on social media. |

---

## 3. Journey B: The Client ("Sarah")
**Persona:** Roblox Game Owner/Entrepreneur.
**Goal:** Acquire high-quality assets with zero risk of "ghosting."
**Scenario:** Hiring a scripter for a new game feature.

| Stage | User Actions | System Touchpoints | Thinking | Emotional State | Opportunities |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **1. Funding** | • Creates Project.<br>• Deposits USDT to fund wallet.<br>• Clicks "Hire". | • **Wallet:** Listens for Blockchain confirmation.<br>• **Escrow:** Freezes funds. | *"That's a lot of money to lock up. I hope this dev is good."* | 😟 Anxious | Show real-time status: "Waiting for Network Confirmation (2/3)." |
| **2. Monitoring** | • Checks dashboard for progress.<br>• Chats with Alex via internal messenger. | • **Messaging:** Secure, logged chat.<br>• **Milestones:** Tracking %. | *"They are replying fast. Good sign."* | 🙂 Reassured | Add "Last Active" timestamps to Developer profiles. |
| **3. Verification** | • Receives "Work Submitted" alert.<br>• Tests the script in-game.<br>• Finds a minor bug. | • **Workflow:** Allows "Request Revision".<br>• **Timer:** Pauses auto-acceptance. | *"It works, but needs a tweak. Glad I didn't release funds yet."* | 🧐 Critical | distinct "Approve" vs "Request Changes" buttons (Red vs Green). |
| **4. Release** | • Verifies fix.<br>• Rates Alex 5 stars.<br>• Closes contract. | • **Escrow:** Releases funds to Alex.<br>• **Reputation:** Updates Sarah's "Client Score". | *"Perfect. I'll hire him again next week."* | 🤝 Satisfied | "One-click Rehire" button for future projects. |

---

## 4. Pain Points & Solutions Matrix
*derived from*

| Pain Point | Current State (Discord/Ad-hoc) | RoDevsy Solution |
| :--- | :--- | :--- |
| **"The Ghost"** | Dev takes 50% upfront and blocks the Client. | **Escrow:** No funds released until work is verified. |
| **"The Chargeback"** | Client pays via PayPal, gets work, then files a dispute. | **Crypto/Credits:** Irreversible transactions on internal ledger. |
| **"The IP Theft"** | Client receives file but refuses to pay. | **File Vault:** Files hosted on RoDevsy; access granted *only* after or during secure flow (depending on config). |