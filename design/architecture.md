# 🏗️ System Architecture

## 1. Overview
*(A high-level description of the system's architecture (e.g., "a microservices-based application hosted on AWS..."). Include a C4-style System Context or Container diagram.)*



## 2. Architectural Drivers
*(What are the key NFRs or business constraints that shape the architecture?)*
- **Driver 1:** (e.g., "High availability: must achieve 99.95% uptime.")
- **Driver 2:** (e.g., "Compliance: must be HIPAA compliant.")

## 3. Architectural Decisions (ADRs)
*(Log key decisions made. This can link to a separate /ADR folder.)*
- **ADR-001:** Use PostgreSQL over MySQL due to superior JSONB support.
- **ADR-002:** Implement asynchronous processing via RabbitMQ for payment processing.

## 4. Component Diagram
*(A diagram showing the major components/services and their interactions.)*



[Image of Component Diagram]


## 5. Data Flow Diagram
*(A diagram showing how data moves through the system, especially for key processes.)*



[Image of Data Flow Diagram]


## 6. Technology Stack
| Layer | Technology | Rationale |
|---|---|---|
| **Frontend** | React, TypeScript | ... |
| **Backend** | Go (Gin), Node.js (Express) | ... |
| **Database** | PostgreSQL, Redis | ... |
| **Infra** | AWS (EKS, S3, RDS) | ... |
