# 🎨 UI/UX Design System & Experience Guidelines: RoDevsy

> **Version:** 1.0
> **Status:** Active Standard
> **Target Audience:** Roblox Developers (Gen Z) & Clients
> **Tech Stack:** React + TailwindCSS + Shadcn/UI + Lucide Icons
> **Theme:** "Dark Mode Finance" (Roblox-Native Aesthetic)

---

## 1. Design Philosophy: "The Bank of the Metaverse"
RoDevsy occupies a critical intersection: it must feel as **familiar as a game** but function with the **integrity of a bank**.

### 1.1 Core Principles
1.  **Clarity Over Coolness (The FinTech Rule):**
    * *Bad:* Neumorphic buttons, low-contrast gradients, hidden menus.
    * *Good:* High-contrast text, clear borders, unambiguous status labels.
    * *Rule:* Financial data (Balances, Fees) must never be truncated.
2.  **Friction is a Feature (The Security Rule):**
    * "One-Click Buy" is great for Amazon, but bad for Escrow.
    * **Critical Actions** (Locking Funds, Releasing Payment) require **High-Friction Interactions** (e.g., "Slide to Confirm" or "Type 'CONFIRM' to release") to prevent accidental loss.
3.  **Trust Signals Everywhere:**
    * Every user avatar must carry a verification badge (if applicable).
    * Every contract status must be visible at a glance.

---

## 2. Visual Identity System

### 2.1 Color Palette (Tailwind Configuration)
*Designed to minimize eye strain during long coding/gaming sessions (Dark Mode Default).*

| Token              | Hex Value | Tailwind Class     | Usage Context                          |
| :----------------- | :-------- | :----------------- | :------------------------------------- |
| **Brand Primary**  | `#0074E0` | `bg-blue-600`      | Primary CTAs (Matches Roblox Blue).    |
| **Brand Surface**  | `#0F172A` | `bg-slate-900`     | Global Background (Not pitch black).   |
| **Brand Card**     | `#1E293B` | `bg-slate-800`     | Cards, Modals, Sidebar.                |
| **Status Success** | `#10B981` | `text-emerald-500` | Available Balance, Verified, Job Done. |
| **Status Danger**  | `#EF4444` | `text-red-500`     | Funds Locked, Dispute, Error.          |
| **Status Warn**    | `#F59E0B` | `text-amber-500`   | Pending, In Review, System Alert.      |
| **Crypto USDT**    | `#26A17B` | `bg-[#26A17B]`     | Specific branding for USDT flows.      |

### 2.2 Typography
* **Font Family:** `Inter` (Google Fonts).
* **Rationale:** It is the industry standard for UI readability and closely mimics the Roblox platform font stack, reducing cognitive load.

### 2.3 Iconography (Lucide React)
* **Wallet:** `Wallet` (Balance), `Landmark` (Escrow).
* **Security:** `ShieldCheck` (Verified), `Lock` (Encrypted/Locked).
* **Actions:** `Gavel` (Dispute), `FileCode` (Lua Script), `FileBox` (Model).

---

## 3. Component Library (Shadcn/UI Extensions)

### 3.1 The "Trust Badge" Avatar
*Crucial for preventing impersonation scams.*
* **Component:** `Avatar` wrapped in a `HoverCard`.
* **Visuals:**
    * **Gold Border:** "Gold Tier" Developer (High Reputation).
    * **Green Check:** Identity Verified (Roblox OAuth + ID).
    * **Red Shield:** Admin/Staff.
    * **Gray/Ghost:** Unverified (Warning Tooltip: "Proceed with Caution").

### 3.2 The Financial Dashboard Card
*The first thing a user sees.*
* **Layout:** Split view.
    * **Left:** "Available Balance" (Big Green Text). *Action: Withdraw.*
    * **Right:** "Frozen in Escrow" (Big Red Text). *Action: View Contracts.*
* **Micro-interaction:** Values are obscured (`****`) until hovered (Privacy Mode for Streamers).

### 3.3 The "Smart Contract" State Bar
*Visualizes the lifecycle of a job (Reference: `functional.md`).*
* **Step 1:** `Draft` (Gray)
* **Step 2:** `Funded & Locked` (Red Pulse Animation)
* **Step 3:** `In Progress` (Blue)
* **Step 4:** `In Review` (Amber)
* **Step 5:** `Released` (Green) or `Disputed` (Purple)

---

## 4. Critical UX Flows & Wireframes

### 4.1 Onboarding: The "Zero Trust" Gate
*Goal: Filter out bots and unverified users immediately.*
1.  **Landing:** Hero text "Secure Escrow for Roblox". CTA: "Login with Roblox".
    * *Visual:* 
2.  **Roblox OAuth:** User is redirected to Roblox, approves, and returns.
3.  **Age Gate (Risk: COPPA):**
    * **Check:** If `RobloxAccountAge < 30 days`, redirect to "Account Too New" error page.
4.  **TOS Acceptance:**
    * **Interaction:** Modal with a sticky "Accept" button at the bottom.
    * **Constraint:** Button is `disabled` until the scrollbar hits the bottom pixel.

### 4.2 Creating a Job: The "Money Lock" Moment
*Goal: Make the user painfully aware that money is leaving their wallet.*
1.  **Form:** Title, Description, Budget.
2.  **Real-Time Fee Calc:**
    * *Input:* 1000 Credits.
    * *Feedback:* "Fee (5%): 50 Credits. **Total Deducted: 1050 Credits.**"
3.  **Confirmation Action:**
    * *Instead of a Button:* Use a **Slide-to-Confirm** slider.
    * *Label:* "Slide to LOCK Funds".
    * *Feedback:* Haptic vibration (mobile) + "Lock Sound" + Red Toast Notification: "Funds Frozen in Escrow."

### 4.3 The Workroom: File Exchange
*Goal: Prevent "File Theft" (IP Risk).*
1.  **Developer View:** Upload `.rbxm` or `.lua` file.
    * *Status:* "Encrypted & Watermarked".
2.  **Client View:**
    * *Before Payment:* See filename and a **Watermarked Thumbnail** (if image). "Download Locked."
    * *After Payment:* "Unlock Animation" (Padlock opens). "Download Ready."

---

## 5. Accessibility (WCAG 2.1 AA) & Mobile Responsiveness

### 5.1 Mobile Optimization
* **Thumb Zone:** Primary actions (Release Funds, Chat Send) must be in the bottom 30% of the screen.
* **Tap Targets:** All buttons minimum `44px` height.
* **Tables:** Do not use wide tables on mobile. Use "Card Lists" for Transaction History.

### 5.2 Accessibility Constraints
* **Focus States:** Every interactive element must have a visible `ring-2 ring-blue-500` on focus.
* **Screen Readers:** Status icons (Check/X) must have `aria-label="Verified User"` or `aria-label="Transaction Failed"`.
* **Color Blindness:** Never rely on color alone.
    * *Bad:* Red text for error.
    * *Good:* Red text + "Exclamation Triangle" icon.

---

## 6. Error Handling & "Anti-Panic" UX
*FinTech users panic easily. Error messages must be calming and precise.*

* **Network Error:** "Connection lost. Your funds are safe. Retrying..." (Not just "Error 500").
* **Transaction Fail:** "Deposit failed. No funds were deducted from your wallet."
* **Dispute Logic:** If a user clicks "Dispute", show a modal: "Wait! Have you tried talking to the developer first? 80% of issues are solved in chat." (Reduces Admin Ticket Load).

---

## 7. Implementation Checklist (Solo Dev)
* [ ] Install `shadcn/ui` base: Button, Card, Input, Dialog, Toast, Avatar, Badge.
* [ ] Configure `tailwind.config.js` with the Brand Colors.
* [ ] Create `Layout.tsx` with the persistent Sidebar and Header (User Badge).
* [ ] Build `AuthGuard.tsx` component to handle redirects for unverified users.