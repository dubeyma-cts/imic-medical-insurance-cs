## Architecture Requirements Specification (ARS) – IMIC

### 1. Business Context & Scope
IMIC provides an Internet portal for **Policyholders** and **Agents**, and an Intranet portal for **Company Staff** to approve/reject policy applications and claims. The policy holder sees **read-only** personal, policy and claim details; the company staff processes approvals; the agent creates policies and claims on behalf of customers. (Source: IMIC case study) 

### 2. Primary Capabilities
- Buy Policy (multi-member, documents upload, premium computation; all-or-nothing approval)
- Claims Submission & Decision (statuses: Sent for approval, Accepted with cheque details, Rejected with reason)
- Authentication and account management (3 failed logins → account lock; manual unlock)
- Consistent look & feel (shared header/footer/left navigation), minimal clicks navigation

### 3. Constraints & Standards
- ASP.NET/.NET 8 stack; RESTful APIs; relational database for core records; secure document storage for uploads.
- Auditability for policy and claims decisions; separation of roles: Policyholder (read-only), Agent (create), Staff (approve).

### 4. Stakeholders & Roles
- Policyholder, Agent, Company Staff, Operations, Security/Compliance, Finance.

### 5. Success Measures
- Time to issue policy and decide claims; portal availability; SLA adherence; correctness of audit and status transparency.

### 6. High-Level Architecture Requirements
- Internet and Intranet applications with authentication and role-based authorization.
- Workflow for policy approval and claims decision.
- Document management for medical reports and proofs, with size limits and retention.
- Notifications (email/SMS) for key events.

### 7. Non-Functional Requirements (summary)
- Availability ≥ 99.9% (critical flows ≥ 99.95%); P95 latency 300–800 ms; RTO ≤ 1h; RPO ≤ 15m; lockout after 3 invalid attempts; encryption in transit & at rest; audit logging for sensitive actions.

### 8. Open Issues
- Choice of DB engine (Azure SQL vs PostgreSQL); queue/messaging; payment integration for premium collection.

## Quality Attribute Scenarios (QAS)

### Availability
**Stimulus:** App Service instance goes down. **Response:** Traffic shifted; health checks pass in < 60s. **Measure:** Monthly uptime â‰¥ 99.9%.

### Performance
**Stimulus:** Agent submits policy with 5 dependents + docs. **Response:** Server validates and persists; returns 202/200 with tracking ID. **Measure:** P95 â‰¤ 2s for submission, â‰¤ 500ms for status reads.

### Security â€“ Lockout
**Stimulus:** User fails login 3 times. **Response:** Account locked; admin unlock only. **Measure:** Lock flag set; audit entry created.

### Auditability
**Stimulus:** Staff accepts claim. **Response:** Write immutable audit record (who, when, reason, cheque ref). **Measure:** 100% of decisions audited.

### Usability
**Stimulus:** Navigate from home to claims status. **Response:** â‰¤ 2 clicks; consistent layout.

## Interface & Integration Requirements

### External Actors
- Notification providers (Email/SMS)
- Optional payment gateway (for premiums)

### Internal Interfaces (illustrative)
- Web Portal ↔ API Gateway: HTTPS + JWT
- API ↔ Policy Service: REST (OpenAPI); create application, compute premium, issue policy
- API ↔ Claims Service: REST; submit claim, update status, enter cheque details

### Non-Functional for Interfaces
- Idempotency for POST; pagination; retry with exponential backoff; request/response size limits (document metadata route separate from binary upload channel).

## Data & Information Requirements

### Master Data
- Policy, Member, Claim, Document, UserAccount, LoginAttempt

### Rules
- Application rejected if any member invalid; claim statuses: Sent for approval/Accepted/Rejected with reason; cheque details recorded on acceptance.

### Storage
- Relational DB for policy/claim core; object/blob storage for documents; index PII in restricted tables; encrypt at rest; backup daily, PITR 15m.

### Limits
- Single document up to 10MB; total claim docs ≤ 50MB.
## Security, Privacy & Compliance Requirements

- Authentication with account lock after 3 failed attempts; manual unlock.
- RBAC: Policyholder (read), Agent (create), Staff (approve).
- TLS 1.2+; HSTS; secure cookies; CSRF protection.
- Encryption at rest (DB/storage). PII/PHI minimization in logs.
- Audit trail for policy & claim decisions.
- Data residency and retention policies (7 years suggested; align to local regulation).
## Deployment, Availability & DR Requirements

- Environments: Dev/Test/Stage/Prod; Stage mirrors Prod topology.
- Blue/Green or rolling deployments with health probes.
- Availability targets: 99.9% overall; 99.95% for approvals.
- DR: Multi-AZ; Warm standby region; **RTO ≤ 1h, RPO ≤ 15m**.
## Risks, Assumptions, Decisions (RAD)

### Risks
- Document storage cost & performance under peak claims.
- Manual unlock process delays.

### Assumptions
- Internet and Intranet apps share identity provider; staff are provisioned centrally.

### Decisions (initial)
- Role separation enforced at route/controller level.
- All uploads virus-scanned before persistence.
## Traceability Matrix (Excerpt)

| Requirement | Source | Design/Component | Test Artifact |
|---|---|---|---|
| All-or-nothing policy approval | IMIC Case Study | Policy Service + Underwriting workflow | UC01 policy buying E2E |
| Claim statuses incl. cheque details | IMIC Case Study | Claims Service, Finance integration | UC02 claim decision tests |
| 3 failed logins → lock | IMIC Case Study | Identity/ASP.NET Core Identity | Auth & Lockout tests |
| Common L&F and minimal clicks | IMIC Case Study | Shared layout components | UX tests |
