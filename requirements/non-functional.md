# ⚖️ Non-Functional Requirements (Enterprise Grade)

## 1. Security & Compliance (FinTech Standard)
**Goal:** Zero-trust architecture protecting user funds (USDT/Credits) and PII, complying with global regulations.

### 1.1 Authentication & Authorization
* **NFR-SEC-01 (MFA):** Mandatory Two-Factor Authentication (TOTP or Email OTP) for any withdrawal request > 500 Credits and for all Admin Panel access.
* **NFR-SEC-02 (Session Management):**
    * Access Tokens (JWT) life: **15 minutes**.
    * Refresh Tokens life: **7 days** (with sliding expiration).
    * **Revocation:** Implementation of a token blacklist (Redis) to instantly kill sessions upon "Logout" or "Security Event."
* **NFR-SEC-03 (Role-Based Access - RBAC):** Strict separation of duties. Developers cannot access Client payment data; Customer Support Agents cannot view decrypted passwords or execute withdrawals.

### 1.2 Data Protection & Encryption
* **NFR-SEC-04 (At Rest):** Database volumes must be encrypted using **AES-256-GCM**. Sensitive columns (Wallet Addresses, Email) must be field-level encrypted via a Key Management Service (KMS).
* **NFR-SEC-05 (In Transit):** Enforce **TLS 1.3** with Perfect Forward Secrecy (PFS). HSTS (HTTP Strict Transport Security) header must be set to `max-age=31536000`.
* **NFR-SEC-06 (Key Management):** Cryptographic keys must be rotated automatically every 90 days. Master keys never leave the Hardware Security Module (HSM) or Cloud KMS.

### 1.3 Vulnerability Management
* **NFR-SEC-07 (Sanitization):** All user inputs (Project Descriptions, Chat) must undergo strict sanitization to prevent XSS. All SQL queries must use Parameterized Statements to prevent SQL Injection.
* **NFR-SEC-08 (Rate Limiting):**
    * Login Endpoints: 5 attempts / minute per IP.
    * API General: 1,000 requests / minute per User ID.
    * DDoS Protection: Implementation of Web Application Firewall (WAF) to block malicious traffic patterns.

### 1.4 Regulatory Compliance (COPPA/GDPR)
* **NFR-SEC-09 (Age Safety):** Since the audience includes minors (Roblox), data collection must minimize PII. "Right to be Forgotten" requests must wipe data from primary DB and anonymize logs within 30 days.

---

## 2. Reliability & Availability
**Goal:** Ensure the platform acts as a reliable financial ledger with zero downtime during transactions.

### 2.1 Availability
* **NFR-REL-01 (SLA):** Target **99.95% availability** (approx. 21 mins downtime/month).
* **NFR-REL-02 (Deployment):** Deployments must utilize **Blue/Green** or **Canary** strategies to ensure zero downtime during updates.
* **NFR-REL-03 (Circuit Breakers):** External dependencies (e.g., Roblox API, Crypto Node, Email Service) must be wrapped in Circuit Breakers (e.g., Resilience4j) to prevent cascading failures.

### 2.2 Disaster Recovery (DR)
* **NFR-REL-04 (RPO - Recovery Point Objective):** < 1 minute. Database Write-Ahead Logs (WAL) must be streamed to a backup location instantly.
* **NFR-REL-05 (RTO - Recovery Time Objective):** < 1 hour. Infrastructure must be defined as Code (Terraform/Ansible) to allow rapid rebuilding of the environment.

---

## 3. Performance & Scalability
**Goal:** Maintain fluidity for gamified elements while ensuring transactional accuracy.

### 3.1 Latency
* **NFR-PER-01 (API):** 95th Percentile (P95) response time for read operations must be **< 150ms**.
* **NFR-PER-02 (Write Operations):** Financial writes (Escrow Lock) must complete within **500ms** (including ACID locking).
* **NFR-PER-03 (Static Assets):** All assets (Images, CSS, JS) must be served via CDN with a global latency of **< 50ms**.

### 3.2 Throughput & Scalability
* **NFR-PER-04 (Concurrency):** System must support **10,000 concurrent active users** (WebSocket connections for Chat/Notifications).
* **NFR-PER-05 (Database):** Primary database must handle **2,000 TPS (Transactions Per Second)**. Read Replicas must be auto-scaled based on CPU utilization (>70%).

---

## 4. Data Integrity & Financial Consistency
**Goal:** Absolute accuracy in the "Credit System." No race conditions allowed.

### 4.1 Transactional Integrity
* **NFR-DAT-01 (ACID):** All wallet balance changes must occur within strict **ACID transactions**. Isolation level must be set to `REPEATABLE READ` or `SERIALIZABLE` to prevent "Double Spend" attacks.
* **NFR-DAT-02 (Idempotency):** All financial API endpoints (Deposit, Withdraw, Pay) must support Idempotency Keys to prevent duplicate processing if a client retries a request due to network failure.
* **NFR-DAT-03 (Audit Trail):** Every change to a `wallet_balance` column must trigger an immutable insert into a `transaction_ledger` table. This ledger must be "Append Only."

---

## 5. Observability & Maintainability
**Goal:** "You can't fix what you can't see." Full visibility into system health.

### 5.1 Logging & Tracing
* **NFR-OBS-01 (Structured Logging):** All logs must be in JSON format including `trace_id`, `user_id`, `severity`, and `component`.
* **NFR-OBS-02 (Distributed Tracing):** Implementation of OpenTelemetry to trace requests across microservices (e.g., from UI -> API -> Auth Service -> Database).
* **NFR-OBS-03 (Metric Alerts):** Critical alerts (e.g., "Wallet Balance Mismatch," "Payment Gateway Error") must trigger PagerDuty notifications to On-Call engineers within **1 minute**.

### 5.2 Code Standards
* **NFR-MNT-01 (Linting):** CI/CD pipelines must block merges that fail strict Linting (ESLint/SonarQube) or contain Security Hotspots.
* **NFR-MNT-02 (API Docs):** OpenAPI (Swagger) documentation must be auto-generated from code annotations and hosted on a developer portal.

---

## 6. Usability & Accessibility
**Goal:** A frictionless experience for young gamers and professional studios alike.

* **NFR-USE-01 (Device Support):** Fully responsive design supporting mobile viewports (down to 320px width) and large desktop monitors (4k).
* **NFR-USE-02 (Accessibility):** Compliance with **WCAG 2.1 Level AA**.
    * All interactive elements must be keyboard navigable.
    * Color contrast ratios must meet 4.5:1 standard.
* **NFR-USE-03 (Error Handling):** Errors must be displayed in "Human Readable" format (e.g., "Insufficient Funds" instead of "Error 500").