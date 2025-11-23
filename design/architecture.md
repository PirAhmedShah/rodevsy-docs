# 🏗️ System Architecture: RoDevsy Platform

> **Version:** 1
> **Status:** Approved
> **Classification:** Confidential / Internal

## 1. Executive Summary
RoDevsy is a **hybrid-cloud financial escrow platform** designed for the Roblox ecosystem. It bridges the trust gap between Developers and Clients by securing funds in a "Zero Trust" ledger until project milestones are cryptographically verified or manually approved.

The architecture strictly separates the **Presentation Layer (Edge)** from the **Financial Core (Private VPC)** to ensure PCI-DSS compliance and maximum security for user funds.

---

## 2. Architectural Principles

1.  **Zero Trust Security:** We assume the network is compromised. Every internal service-to-service call (e.g., API to Database) requires mutual authentication (mTLS) or strict IAM policies.
2.  **Immutability of Ledger:** Financial records (Credits) are **append-only**. We never `UPDATE` a balance row directly without a corresponding `Transaction` audit log entry.
3.  **Contract-Driven Type Safety:** Since the Frontend and Backend live in separate repositories, we enforce type safety via **OpenAPI (Swagger)**. The Backend generates a specification, and the Frontend auto-generates TypeScript clients from that spec, ensuring `rodevsy-web-app` is always in sync with `rodevsy-api-server`.
4.  **Event-Driven Consistency:** Critical but slow operations (e.g., Blockchain confirmations, Email notifications) are decoupled using an asynchronous Message Queue (BullMQ/Redis) to keep the API response time under **100ms**.

---

## 3. High-Level System Context (C4 Level 1)

* **Public Zone (Internet):**
    * **Users:** Access via Web (Desktop/Mobile).
    * **Roblox Game Server:** Communicates via API keys to verify asset ownership.
* **Edge Zone (Vercel):**
    * **Frontend App (`rodevsy-web-app`):** Serves static assets and handles Server-Side Rendering (SSR).
* **Private Zone (AWS VPC):**
    * **API Cluster (`rodevsy-api-server`):** Handles logic.
    * **Worker Cluster:** Handles background jobs.
    * **Persistence:** Databases and File Storage.

---

## 4. Detailed Technology Stack

### 4.1. Repository Structure (Polyrepo)
The system is split into two distinct repositories to decouple deployment cycles and allow independent scaling.

* **`rodevsy-api-server` (Backend):** Contains the NestJS source, Prisma schemas, and Docker configurations.
* **`rodevsy-web-app` (Frontend):** Contains the Next.js source, UI components, and E2E tests.

**Synchronization Strategy:**
* The Backend exposes a `/api-json` (Swagger) endpoint.
* The Frontend utilizes a codegen tool (e.g., `orval` or `openapi-typescript`) to generate strict TypeScript interfaces and API hooks based on the Backend's live specification.

### 4.2. Layer 1: Presentation (Frontend)
**Repository:** `rodevsy-web-app`
**Host:** Vercel

| Component         | Technology                   | Reasoning                                                                                                                                  |
| :---------------- | :--------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------- |
| **Framework**     | **Next.js 14+ (App Router)** | Best-in-class SSR and SEO. React Server Components (RSC) reduce bundle size.                                                               |
| **Language**      | **TypeScript**               | Strict typing prevents `undefined` runtime errors.                                                                                         |
| **UI Library**    | **shadcn/ui**                | Built on **Radix Primitives**. Guarantees WCAG 2.1 compliance (Screen reader support, Keyboard nav) which is legally required for Fintech. |
| **Styling**       | **Tailwind CSS**             | Atomic CSS for rapid development and low CSS bundle size.                                                                                  |
| **State**         | **Zustand**                  | Minimalist client-state (Auth User, UI Toggles).                                                                                           |
| **Data Fetching** | **TanStack Query**           | Handles caching, deduplication, and "stale-while-revalidate" logic for Wallet Balances.                                                    |
| **Validation**    | **Zod**                      | Schema validation for forms *before* network requests.                                                                                     |
| **Testing**       | **Vitest**                   | Native Vite integration; runs 5x faster than Jest for React components.                                                                    |

### 4.3. Layer 2: API & Business Logic (Backend)
**Repository:** `rodevsy-api-server`
**Host:** AWS ECS (Fargate) or App Runner

| Component        | Technology                    | Reasoning                                                                                             |
| :--------------- | :---------------------------- | :---------------------------------------------------------------------------------------------------- |
| **Framework**    | **NestJS**                    | Modular architecture (Modules, Guards, Interceptors) enforces clean code.                             |
| **HTTP Adapter** | **Fastify**                   | **Crucial:** Processes ~30k req/sec (vs Express ~15k). Low overhead for high-frequency trading logic. |
| **Lang**         | **Node.js 20 (LTS)**          | Stable, widely supported runtime.                                                                     |
| **Real-time**    | **Socket.io + Redis Adapter** | Powers the "Dispute Chat" across multiple server instances.                                           |
| **Logging**      | **Pino**                      | Asynchronous, structured JSON logging. Essential for high-load systems.                               |
| **Queue**        | **BullMQ (Redis)**            | Offloads email sending and blockchain polling to background workers.                                  |
| **Security**     | **Helmet / Throttler**        | Auto-sets HSTS headers; Distributed Rate Limiting to stop DDoS.                                       |
| **Testing**      | **Jest**                      | Deep integration with NestJS Dependency Injection for mocking services.                               |

### 4.4. Layer 3: Data Persistence (The Vault)
**Location:** AWS Private Subnets

| Component        | Technology                 | Reasoning                                                                                                                     |
| :--------------- | :------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| **Primary DB**   | **PostgreSQL 16 (Aurora)** | ACID Compliance is non-negotiable for financial ledgers. JSONB support allows flexible "Asset Metadata".                      |
| **ORM**          | **Prisma**                 | Type-safe queries. **Note:** Must use `Prisma Accelerate` or AWS RDS Proxy for connection pooling in serverless environments. |
| **Cache**        | **Redis 7 (ElastiCache)**  | Caches User Sessions, Rate Limits, and temporary Chat History.                                                                |
| **Object Store** | **AWS S3**                 | Stores Game Assets (`.rbxm`) and Dispute Evidence images.                                                                     |

---

## 5. Critical Architecture Designs

### 5.1. The "Double-Entry" Ledger System
We **never** simply update a user's balance. We use a transactional ledger approach.

**Database Schema Concept:**
```typescript
model Wallet {
  id        String   @id @default(uuid())
  userId    String   @unique
  balance   Decimal  @default(0.00) @db.Decimal(20, 4) // NEVER use Float
  frozen    Decimal  @default(0.00) @db.Decimal(20, 4)
  version   Int      @default(0) // Optimistic Locking
}

model Transaction {
  id        String   @id @default(uuid())
  walletId  String
  amount    Decimal
  type      TxType   // DEPOSIT, ESCROW_HOLD, RELEASE, FEE
  reference String   // Project ID or Stripe Charge ID
  createdAt DateTime @default(now())
}