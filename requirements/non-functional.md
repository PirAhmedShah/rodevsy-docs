# Non-Functional Requirements

## 1. Security & Compliance (FinTech Standard)
* **NFR-001 (MFA):** Mandatory Two-Factor Authentication (TOTP or Email OTP) for all withdrawal requests > 500 Credits and all administrative dashboard access.
* **NFR-002 (Identity Assurance):** Every session must be linked to a verified Roblox identity via OAuth 2.0. Unverified identities are restricted from escrow/marketplace participation.
* **NFR-003 (Session Management):** JWT Access Tokens expire in 15 minutes; Refresh Tokens in 7 days. Use Redis-based blacklisting to revoke sessions instantly upon security events or logout.
* **NFR-004 (RBAC):** Strict separation of duties; developers cannot access client payment data, and support agents cannot execute fund withdrawals.
* **NFR-005 (Data Encryption):** Database volumes encrypted via AES-256-GCM. PII and wallet addresses require field-level encryption via Cloud KMS.
* **NFR-006 (Transport Security):** Enforce TLS 1.3 with Perfect Forward Secrecy. HSTS must be enabled (`max-age=31536000`).
* **NFR-007 (Vulnerability Management):** All user-uploaded assets (Marketplace/Projects) must pass automated scanning for backdoors/malicious code.
* **NFR-008 (Input Sanitization):** Strict XSS sanitization for all content (Projects, Forum, Chat) and Parameterized Queries for all SQL operations.
* **NFR-009 (Privacy & Regulatory):** Comply with COPPA/GDPR via data minimization and an automated "Right to be Forgotten" workflow for data deletion/anonymization.

## 2. Reliability & Availability
* **NFR-010 (Availability SLA):** Target 99.95% uptime to ensure Escrow and Marketplace accessibility.
* **NFR-011 (Deployment):** Use Blue/Green or Canary deployment strategies to maintain zero-downtime during updates.
* **NFR-012 (Resilience):** Wrap external Roblox API and Crypto Gateway dependencies in Circuit Breakers to prevent cascading failures.
* **NFR-013 (Disaster Recovery):** RPO < 1 minute via streaming Write-Ahead Logs (WAL); RTO < 1 hour via Infrastructure-as-Code (Terraform).

## 3. Performance & Scalability
* **NFR-014 (Read Latency):** P95 response time for core navigation (Marketplace, Talent Directory, Forum) must be < 150ms.
* **NFR-015 (Transactional Latency):** Escrow Lock/Release operations must complete within 500ms, ensuring ACID consistency.
* **NFR-016 (CDN Performance):** All static content (Asset previews, UI components) must be served via global CDN with < 50ms latency.
* **NFR-017 (Concurrency):** Support 10,000+ concurrent active users via scalable WebSocket clusters for real-time notifications and chat.
* **NFR-018 (Database Throughput):** Scale read replicas automatically to handle 2,000+ TPS and CPU utilization > 70%.

## 4. Financial Integrity (Ledger Consistency)
* **NFR-019 (ACID Transactions):** All balance updates must use `SERIALIZABLE` isolation to prevent "Double Spend" attacks during escrow transfers.
* **NFR-020 (Idempotency):** All financial API endpoints (Deposits, Pay, Withdraw) must require Idempotency Keys to prevent duplicate processing.
* **NFR-021 (Immutable Ledger):** Every wallet change must trigger an immutable, append-only record in the `transaction_ledger` table for auditability.

## 5. Observability & Maintainability
* **NFR-022 (Observability):** Structured JSON logging with `trace_id` and `user_id` across all services, integrated with OpenTelemetry for distributed tracing.
* **NFR-023 (Alerting):** Critical system failures (e.g., "Ledger Mismatch") must trigger PagerDuty notifications to engineers within < 1 minute.
* **NFR-024 (CI/CD Quality):** Enforce strict SonarQube/Linting gates; automated builds must block security hotspots.
* **NFR-025 (Documentation):** API documentation (Swagger/OpenAPI) must be auto-generated and maintained on a centralized internal portal.

## 6. Usability & Accessibility
* **NFR-026 (Responsive Design):** Mobile-first design supporting viewports from 320px to 4k desktops, essential for the mobile-heavy Roblox user base.
* **NFR-027 (Accessibility):** WCAG 2.1 Level AA compliance for all community-facing interfaces (Forum/Marketplace).
* **NFR-028 (Clarity):** Errors must be displayed in "Human Readable" format, avoiding technical jargon to assist younger users.