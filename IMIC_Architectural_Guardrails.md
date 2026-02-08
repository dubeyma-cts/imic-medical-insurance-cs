# IMIC – Architectural Guardrails
This document defines **architectural guardrails** (non‑negotiable constraints + recommended practices) to keep the IMIC solution **secure, consistent, resilient, and maintainable** while implementing the functional and non‑functional requirements.
> These guardrails align to IMIC’s stated stakeholder needs (Policy purchase via agent, claims via agent, company approvals, portal read-only views) and confirmed security/NFR expectations (lockout, encryption, audit logging, notifications, performance, availability).
---
## 1. Guardrail Principles
### G1. Security-by-default
- Any new endpoint or UI capability must default to **deny** and be explicitly enabled by role/permission.
- Authentication/authorization is mandatory for all state-changing operations (create/submit/approve/reject/pay).
### G2. Traceability and auditability
- All critical actions must be traceable back to a **user, role, time, and correlation id**.
### G3. Consistency and usability
- UI must follow consistent layout/navigation rules (shared header/footer/left nav; minimum-click navigation).
### G4. Operational readiness
- Every service must be observable (logs/metrics/traces) and meet agreed uptime and response SLAs.
---
## 2. Identity, Authentication & Session Guardrails
### G-IA-01. Strong authentication boundaries
- Distinct identities and sessions for **Policy Holder**, **Agent**, and **Company Staff** portals.
- **Username policy:** user-defined and must be **email or mobile number**; enforce uniqueness.
### G-IA-02. Account lockout enforcement
- Enforce **account lock after 3 consecutive invalid attempts** for all login types.
- Treat unknown usernames as invalid attempts (prevents enumeration).
- Lock is **per-username** and persists until **manual reactivation**.
- UI shows remaining attempts and lock message is on-screen; notify via email to user + security mailbox.
### G-IA-03. Manual reactivation controls
- Only **System Admin** can unlock; verify identity via registered email + admin approval; force password change at next login; audit log the unlock.
---
## 3. Authorization (RBAC/ABAC) Guardrails
### G-AUTHZ-01. Role-based access control (RBAC)
- Minimum roles: `PolicyHolder`, `Agent`, `CompanyStaff`, `Admin`.
- Server-side authorization checks are mandatory (never rely on UI-only restrictions).
### G-AUTHZ-02. Ownership and row-level security
- Agents can view and operate **only** on their own policies/claims portfolio.
- Company staff access is by workflow queues (submitted applications/claims), not by arbitrary search unless explicitly approved.
### G-AUTHZ-03. Read-only policy holder portal
- Policy holder screens are **view-only** for personal, policy, and claim details.
### G-AUTHZ-04. Deep-link safety
- Deep links may be supported but must always enforce authorization on the server.
---
## 4. Data & Document Security Guardrails
### G-DATA-01. Encryption standards
- **TLS 1.2+** for all in-transit communication; **AES-256** for data at rest (DB + document store).
### G-DATA-02. Sensitive document handling
- Only **PDF** and **JPG** are accepted for uploads; reject all other formats.
- Required documents must be complete before submission (no partial submission).
- Documents are validated using **automated validation + manual approval** process.
### G-DATA-03. Data retention and purge
- Retain claims/documents for **7 years** and purge after the retention window; audit purge actions.
### G-DATA-04. PII/PHI minimization
- Notifications must contain minimal sensitive data; avoid attaching documents to SMS.
---
## 5. Domain & Workflow Guardrails (Functional)
### G-DOM-01. Policy purchase channel
- Policy purchase is **agent-only** in Phase 1 (no direct policy holder purchase flow).
### G-DOM-02. Policy composition constraints
- One active policy per customer.
- Maximum **5 dependents**; relationships restricted to **spouse, children, parents**.
### G-DOM-03. Cover and premium rules
- Default cover limits: **₹5,00,000–₹25,00,000**, configurable by admin.
- Premium rules are configurable; premium must be calculated server-side and re-calculated when member details change before approval.
### G-DOM-04. Submission and immutability
- Payment must be captured at submission time; payment mode is **cheque only (Phase 1)**.
- After submission, policy details become immutable (no edits).
### G-DOM-05. Approval rules
- If any member fails validation, **entire policy application is rejected**.
- Rejection must use standardized reason list with optional remarks.
- No staff override of rejection rules.
### G-DOM-06. Claims constraints
- Claims can be submitted through agent; allow dependent claims but route under same policy.
- Limit **3 claims per policy year**.
- Claims are allowed only at **network hospitals (Phase 1)**.
- Scanned copies are sufficient for submission.
- Claim approval SLA is **7 working days**; accepted claim payments are by cheque.
---
## 6. Audit, Logging & Non‑Repudiation Guardrails
### G-AUD-01. Audit logging requirements
- Audit log all approve/reject actions for policy and claims.
- Audit log account unlock operations and administrative configuration changes.
### G-AUD-02. Log quality
- Logs must include: `userId`, `role`, `action`, `entityId`, `timestamp`, `result`, `correlationId`, `sourceIp`.
- Store logs in centralized platform with restricted access and tamper resistance.
---
## 7. Notification Guardrails
### G-NOTIF-01. Status change notifications
- Send notifications for policy/claim status changes via **Email & SMS**.
- Ensure idempotent notification dispatch (avoid duplicates on retry).
### G-NOTIF-02. Security notifications
- Account lockout notifications via email to user and security mailbox; on-screen message shown.
---
## 8. UI/UX & Navigation Guardrails
### G-UX-01. Consistent layout
- All pages must share common header, footer, and left navigation panel.
### G-UX-02. Minimum clicks
- Any primary screen must be reachable in **≤ 2 clicks** from the respective home page.
### G-UX-03. Role-based navigation
- Navigation must hide unauthorized menu items and highlight current location.
---
## 9. Performance, Availability & Operations Guardrails
### G-PERF-01. Response time SLA
- p95 **≤ 2 seconds** for login, dashboard, and list views under normal load.
- Normal load definition: **200 concurrent users**, **20 TPS** peak portal actions.
- Same SLA for intranet and internet portals in Phase 1.
### G-PERF-02. Concurrency and error rate
- Support **500 concurrent sessions** with **< 1% HTTP error rate** during peak tests; include critical transactions (login, policy status list, claims list, approval queues).
### G-OPS-01. Availability target
- Availability target: **99.5% monthly uptime**.
### G-OPS-02. Monitoring and alerting
- Centralized logs + alerts for errors, latency, and lockouts.
---
## 10. API & Integration Guardrails
### G-API-01. Idempotency
- Submission endpoints (policy submit, claim submit, approve/reject) must be idempotent using `Idempotency-Key` or server-side dedup keys.
### G-API-02. Input validation
- Validate all inputs server-side: cover amount range, relationship types, document types, claim limits, network hospital checks.
### G-API-03. Error handling
- Do not leak sensitive information in errors (e.g., “user not found”); use consistent error messages.
---
## 11. Change Control & Compliance Guardrails
### G-CHG-01. Controlled configuration
- Premium rules and cover limits are configurable; changes require admin role and must be audited.
### G-CHG-02. Security review gates
- Any change that affects authentication, authorization, encryption, or logging requires security review and regression testing against lockout, audit, and notification rules.
---
## 12. Definition of Done (DoD) – Guardrail Checks
A feature is **Done** only if:
1. RBAC enforced server-side and verified with negative tests.
2. Audit logs exist for critical actions and include required fields.
3. Data in transit and at rest encryption is validated.
4. Performance/availability targets met for impacted journeys.
5. Notifications behave as specified (email/SMS + lockout alerts).
---
## Appendix: Mappings to Requirements
- **Security lockout & manual unlock:** 3 invalid attempts lock; manual reactivation only; notify user/security mailbox; show remaining attempts.
- **Encryption & retention:** TLS 1.2+; AES-256; retain 7 years and purge.
- **UI consistency & navigation:** shared header/footer/left nav; ≤2 clicks to primary screens.
- **Performance & operations:** p95 ≤ 2s; 500 concurrent; <1% errors; 99.5% uptime; centralized logs + alerts.
