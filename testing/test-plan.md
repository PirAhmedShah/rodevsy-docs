# 🧪 Master Test Plan: RoDevsy Platform

> **Version:** 1.0
> **Status:** Draft
> **Scope:** MVP Launch (Escrow Core, Identity, Financial Ledger)
> **Reference:** Architecture v1.1, Functional Specs v1.0

## 1. Introduction & Objectives
This document outlines the validation strategy for the RoDevsy "Zero Trust" escrow platform.
**Primary Objective:** Verify that no funds can be lost, stolen, or locked indefinitely due to race conditions or logic errors.
**Secondary Objective:** Ensure compliance with mTLS security constraints and Roblox OAuth integration.

## 2. Scope of Testing

### 2.1 In Scope (Critical Path)
* **Financial Core:** Deposit (USDT/Crypto), Internal Ledger Transfers, Withdrawal Requests.
* **Escrow Logic:** Contract Creation, Fund Locking, Milestone Verification, Release/Refund logic.
* **Identity:** Roblox OAuth Binding, Role-Based Access Control (RBAC).
* **Security:** API Authentication (JWT), Rate Limiting, SQL Injection prevention.

### 2.2 Out of Scope (Phase 1)
* **Game Asset Marketplace UI:** (Visual layout testing is lower priority than backend logic).
* **Community Forums:** Social features are non-critical.
* **Third-Party Analytics:** Integration with tools like Google Analytics.

---

## 3. Test Strategy & Levels

### 3.1 Unit Testing (Code Level)
* **Focus:** Individual functions (e.g., `calculateFees()`, `validateRobloxId()`).
* **Tooling:** `Jest` (TypeScript).
* **Coverage Target:** > 90% for `services/finance` and `services/escrow`; > 80% global.
* **Critical Assertion:** All financial calculations must use integer math (no floating point errors).

### 3.2 Integration Testing (API Level)
* **Focus:** Interaction between microservices (e.g., API Gateway $\to$ Ledger Service).
* **Tooling:** `Supertest`, `Mocha`.
* **Key Scenario:**
    * **Race Condition Test:** Simulate 50 simultaneous "Withdraw" requests for the same wallet to ensure `Optimistic Locking` (Architecture 5.3) works and only 1 succeeds.

### 3.3 End-to-End (E2E) Testing (User Flow)
* **Focus:** Full user journeys simulating real browser behavior.
* **Tooling:** `Cypress` or `Playwright`.
* **Scenarios:**
    1.  **"Happy Path":** User A hires User B $\to$ Funds Locked $\to$ Work Submitted $\to$ Funds Released.
    2.  **"Dispute Path":** User A hires User B $\to$ Dispute Opened $\to$ Admin Resolves $\to$ Partial Refund.

### 3.4 Security Penetration Testing (SAST/DAST)
* **Focus:** Vulnerability scanning.
* **Tooling:** `OWASP ZAP`, `SonarQube`.
* **Mandatory Checks:**
    * Attempting to bypass OAuth.
    * Attempting to inject SQL into search fields.
    * Verifying that mTLS certificates are actually enforced between services.

---

## 4. Test Environment & Data

| Environment | Purpose | Data Strategy | Access |
| :--- | :--- | :--- | :--- |
| **Local** | Dev Unit Testing | Mocks/Stubs | Developers |
| **Staging** | Integration/E2E | Anonymized Production Dump | QA / Auto-CI |
| **UAT** | User Acceptance | Clean Seed Data (Testnet Crypto) | Product Owners |

**Test Data Requirements:**
* **Roblox Accounts:** 10 pre-verified "Test Dummy" Roblox accounts.
* **Crypto Wallets:** Testnet wallets with dummy USDT for deposit simulation.

---

## 5. Entry & Exit Criteria

### Entry Criteria (To start System Testing)
* Build passes CI pipeline (Linting, Unit Tests).
* Database schema version matches `v1.0` migration scripts.
* mTLS certificates deployed to Staging environment.

### Exit Criteria (To Release to Production)
* **Zero** Critical/High Severity bugs (P0/P1).
* 100% Pass rate on "Money Movement" regression suite.
* Performance: API response time < 200ms for 95th percentile under load.
* Security Sign-off: Clean report from OWASP ZAP scan.

---

## 6. Defect Management Process
* **Platform:** Jira / GitHub Issues.
* **Severity Definition:**
    * **P0 (Blocker):** Data loss, Security breach, Financial calculation error. *Immediate Fix.*
    * **P1 (Critical):** Feature broken, no workaround. *Fix before release.*
    * **P2 (Major):** Feature broken, workaround exists.
    * **P3 (Minor):** UI/Cosmetic glitch.

---

## 7. Risks & Mitigation
* **Risk:** Roblox API Rate Limits blocking tests.
    * *Mitigation:* Mock Roblox API responses for 90% of test runs; use live API only for specific E2E suites.
* **Risk:** Testnet Blockchain congestion.
    * *Mitigation:* Mock the blockchain "Confirmation" webhook for internal logic testing.