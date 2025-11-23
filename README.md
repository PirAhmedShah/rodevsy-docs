# 📘 RoDevsy Documentation Hub

> **Project Name:** RoDevsy  
> **Domain:** FinTech / Roblox Escrow & Asset Marketplace  
> **Status:** Active Development  
> **Latest Architecture Version:** 1.1

## 📖 Overview

Welcome to the central documentation repository for **RoDevsy**, a hybrid-cloud financial escrow platform designed specifically for the Roblox ecosystem.

**The Problem:** The current Roblox development market relies heavily on unregulated platforms (e.g., Discord servers), leading to a high volume of scams where developers aren't paid, or clients receive unsatisfactory work.

**The Solution:** RoDevsy bridges this trust gap by acting as a secure intermediary. We provide a "Zero Trust" ledger system that holds funds in escrow until project milestones are cryptographically verified or manually approved, alongside a secure marketplace for game assets.

---

## 📂 Repository Structure

This repository acts as the "Single Source of Truth" (SSOT) for the Software Development Life Cycle (SDLC). Below is the guide to navigating the artifacts.

### 📍 Root Documents
High-level strategic documents covering cross-cutting concerns.
- **[`risk-register.md`](./risk-register.md)**: Logs active project risks, mitigation strategies, and severity levels.
- **[`user-journey.md`](./user-journey.md)**: End-to-end flow of the user experience from onboarding to transaction completion.
- **[`preliminary-terms-of-service.md`](./preliminary-terms-of-service.md)**: Draft legal framework defining user liabilities, asset warranties, and binding dispute resolution logic.

### 🏢 /business (The "Why")
Strategic context, market analysis, and definitions of success.
- **[`vision.md`](./business/vision.md)**: The core mission, strategic goals (KPIs), and "Non-Goals" to maintain scope focus.
- **[`problem-statement.md`](./business/problem-statement.md)**: Detailed analysis of the current market gaps, specific scam vectors, and business opportunities.
- **[`personas.md`](./business/personas.md)**:
  - **Persona 1:** The Developer ("The one who works hard but gets no money").
  - **Persona 2:** The Client ("I need an asset, but I don't know who to trust").
- **[`stakeholders.md`](./business/stakeholders.md)**: Register of key players (Founders, Roblox Corp, End Users) and their influence/interest matrices.

### 📋 /requirements (The "What")
Detailed specifications defining system behavior and constraints.
- **[`functional.md`](./requirements/functional.md)**: Core features including User Auth (FR-001), Escrow Logic, and Marketplace workflows.
- **[`non-functional.md`](./requirements/non-functional.md)**: Enterprise-grade constraints focusing on Security (PCI-DSS, MFA), Observability, and Maintainability.
- **[`use-cases.md`](./requirements/use-cases.md)**: Formalized actor-system interactions (e.g., UC-001 Registration, UC-006 Dispute Resolution).
- **[`glossary.md`](./requirements/glossary.md)**: Definitions of domain-specific terms (e.g., "Credits", "Escrow Balance").

### 🏗️ /design (The "How")
Technical blueprints and visual guides.
- **[`architecture.md`](./design/architecture.md)**: System design utilizing a "Zero Trust" model. Covers:
  - Separation of Presentation Layer (Edge) and Financial Core (Private VPC).
  - Concurrency handling (Optimistic Locking & Idempotency).
  - Ledger immutability.
- **[`ui-ux.md`](./design/ui-ux.md)**: Wireframes, user interface flows, and design system guidelines.

### 🧪 /testing (The "Proof")
Quality assurance artifacts.
- **[`test-plan.md`](./testing/test-plan.md)**: The master strategy for Unit, Integration, and E2E testing.

### 📝 /backlog (The "Work")
Project management tracking.
- **[`product-backlog.md`](./backlog/product-backlog.md)**: Prioritized list of Epics, User Stories, and Tasks pending implementation.

---

## 🔑 Key Architectural Highlights

As detailed in `design/architecture.md` and `requirements/non-functional.md`, RoDevsy enforces strict technical standards:

1.  **Security First:** Implementation of mTLS for service-to-service calls and strict IAM policies.
2.  **Financial Integrity:** Balances are never strictly `UPDATED`; the system uses an append-only `Transaction` log to ensure a traceable audit trail.
3.  **Concurrency Control:** Uses database-level constraints (`CHECK balance >= 0`) and Redis-based Idempotency keys to prevent double-spending or race conditions.
4.  **Compliance:** Adheres to WCAG 2.1 Level AA for accessibility and standard FinTech data protection regulations (AES-256-GCM encryption).

## 🚀 Getting Started

To contribute to this documentation:

1.  **Business Analysts:** Focus on `/business` and `/requirements`. Ensure KPIs in `vision.md` align with stories in `backlog`.
2.  **Architects/Devs:** Maintain `design/architecture.md`. Ensure ADRs (Architectural Decision Records) are updated when tech stack changes occur.
3.  **QA Engineers:** Map Test Cases in `/testing` directly to Use Cases in `/requirements`.

---

*For access to the source code or deployment pipelines, please refer to the `rodevsy-api-server` and `rodevsy-web-app` repositories.*

## 📞 Contact & Support
For legal inquiries: `legal@rodevsy.com`
For support: `support@rodevsy.com`
