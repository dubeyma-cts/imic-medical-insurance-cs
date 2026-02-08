# IMIC - RTM + STRIDE Threat-to-Control Mapping

Generated using **confirmed** stakeholder clarifications and the defined Functional/NFR use cases.

## A) Functional Requirements

### A1. Requirements Traceability Matrix (RTM) - Functional

| FR ID | UC ID(s) | Requirement Area | Confirmed Requirement | Test Scenario (Example) | Stakeholder | Status |
|---|---|---|---|---|---|---|
| FR-01 | UC-05 | Policy Purchase | No, only one active policy per customer is allowed. | Create two policies for same customer; second submission blocked with validation message. | Insurance Company | Confirmed |
| FR-02 | UC-05 | Policy Purchase | Not in Phase 1; agent-only purchase. | Attempt to create policy from Policy Holder portal without agent; system disallows/does not expose flow. | Business | Confirmed |
| FR-03 | UC-05 | Policy Members | Maximum 5 dependents per policy. | Add >5 dependents; system prevents save/submit. | Insurance Company | Confirmed |
| FR-04 | UC-05 | Policy Members | Allowed relationships: spouse, children, parents only. | Add dependent with unsupported relationship; system rejects. | Business | Confirmed |
| FR-05 | UC-05 | Documentation | PDF and JPG only. | Upload documents in PDF/JPG succeeds; upload PNG/DOCX fails. | IT / Architecture | Confirmed |
| FR-06 | UC-05 | Documentation | All required documents must be uploaded before submission. | Submit application with missing required doc; submission blocked until all docs attached. | Insurance Company | Confirmed |
| FR-07 | UC-07 | Documentation | Automated validation followed by manual approval by company staff. | Run automated document validation; verify staff sees results and can approve/reject. | Operations | Confirmed |
| FR-08 | UC-05 | Policy Cover | Default - Min ₹5,00,000; Max ₹25,00,000. Should be configurable | Set cover below min/above max; blocked; verify limits configurable. | Business | Confirmed |
| FR-09 | UC-05 | Premium Calculation | Yes, via admin configuration. | Change premium rule config; new applications use updated premium. | IT / Business | Confirmed |
| FR-10 | UC-05 | Premium Calculation | Yes, premium is recalculated automatically. | Update member age/cover before approval; premium recalculates. | Business | Confirmed |
| FR-11 | UC-06 | Payment | Before approval at submission time. | Attempt to submit without payment; blocked; verify payment captured at submission. | Finance | Confirmed |
| FR-12 | UC-06 | Payment | Cheque only (Phase 1). | Select payment mode; only Cheque is available in Phase 1. | Finance | Confirmed |
| FR-13 | UC-07 | Policy Approval | No, rejection of any member rejects entire policy. | Reject one dependent during review; entire policy becomes Rejected. | Insurance Company | Confirmed |
| FR-14 | UC-07 | Policy Approval | Select from predefined list with optional remarks. | Reject policy; require selecting reason from list; optional remarks accepted. | Operations | Confirmed |
| FR-15 | UC-07 | Policy Issuance | Email only. | Approve policy; verify policy document delivered via email notification. | Operations | Confirmed |
| FR-16 | UC-07 | Credentials | User defined and username should be email or mobile number | Set username as email/mobile; system validates format and uniqueness. | IT | Confirmed |
| FR-17 | UC-09 | Policy Holder Portal | No, view-only access. | Policy holder tries to edit personal details; UI/API denies (read-only). | Business | Confirmed |
| FR-18 | UC-11 | Claims | Maximum 3 claims per policy year. | Submit 4th claim within policy year; system blocks. | Insurance Company | Confirmed |
| FR-19 | UC-11 | Claims | Yes, but routed through the same policy. | Submit claim for dependent; routed and tracked under same policy. | Business | Confirmed |
| FR-20 | UC-11 | Claims | Network hospitals only in Phase 1. | Submit claim for non-network hospital; system blocks or marks invalid. | Insurance Company | Confirmed |
| FR-21 | UC-11 | Claims Documents | Scanned copies sufficient for submission. | Upload scanned copies; submission succeeds without requiring originals. | Operations | Confirmed |
| FR-22 | UC-12 | Claim Processing | 7 working days from submission. | Measure claim approval turnaround; ensure <=7 working days SLA reporting. | Operations | Confirmed |
| FR-23 | UC-12 | Claim Payment | Cheque issued to policy holder. | Approve claim; system captures cheque details and sets payment method cheque. | Finance | Confirmed |
| FR-24 | UC-08 | Agent Portal | No, agents can view only their own policies. | Agent A tries to view Agent B policies; access denied. | Business | Confirmed |
| FR-25 | UC-05 | Agent Portal | No, editing is disabled after submission. | Try editing after submission; fields disabled; API rejects update. | Business | Confirmed |
| FR-26 | UC-07 | Company Portal | No overrides allowed. | Company staff attempts override of rejection rule; system prevents override action. | Compliance | Confirmed |
| FR-27 | UC-01 | Security | Yes, via on-screen message only. | Trigger account lock; verify on-screen message is displayed. | Security | Confirmed |
| FR-28 | UC-07, UC-12 | Audit | Yes, all approve/reject actions. | Approve/reject policy and claim; verify audit log contains user, time, action. | Compliance | Confirmed |
| FR-29 | UC-07, UC-12 | Notifications | Email & SMS | Policy/claim status changes; verify email and SMS notifications sent. | Business | Confirmed |

### A2. STRIDE Threat-to-Control Mapping - Functional

| Use Case / Feature | Asset | STRIDE | Threat | Controls | Trace (Req IDs) |
|---|---|---|---|---|---|
| Login (Policy Holder/Agent/Company) – UC-01/02/03 | User account + session | S, D | Credential stuffing / brute-force attempts leading to account compromise or service degradation. | Account lock after 3 consecutive invalid attempts; count invalid attempts incl. unknown username; per-username scope; show remaining attempts; lock notifications email to user & security mailbox; on-screen lock message. | FR-27; NFR-SEC-01 |
| Login – UC-01/02/03 | Authentication endpoint | I | Username enumeration via different error messages or timing differences. | Use uniform error messages and similar response timing; treat unknown usernames as invalid attempts per policy. | NFR-SEC-01 |
| Create Policy Holder & Application – UC-05 | Policy application + member data | T, E | Agent tampers with member count/relationship rules or creates policy for an unauthorized customer. | Server-side validation: max 5 dependents; allowed relationships (spouse/children/parents); agent-only purchase; restrict to one active policy per customer; RBAC so only Agent role can create applications. | FR-01, FR-02, FR-03, FR-04 |
| Document Upload & Validation – UC-05/07 | Uploaded medical/age proof documents | T, I | Upload of malicious/unsupported files; tampering with diagnostic evidence; leakage of sensitive documents. | Allow only PDF/JPG; mandatory upload before submission; automated validation followed by manual approval; encrypt documents at rest and in transit (system-wide). | FR-05, FR-06, FR-07; NFR-SEC-03 |
| Premium Calculation – UC-05 | Premium pricing rules | T | Client-side manipulation of premium/cover to reduce payable amount. | Compute premium server-side using configurable rules; recalculate premium on member updates prior to approval; enforce cover min/max default and configurable. | FR-08, FR-09, FR-10 |
| Submit Policy Application – UC-06 | Submission workflow + payment intent | T, R | Submission without payment or replay submission causing duplicates. | Require payment at submission time; cheque-only mode (Phase 1); implement idempotent submission (same application cannot be submitted twice). | FR-11, FR-12 |
| Approve/Reject Policy – UC-07 | Approval decision + policy status | R, E | Staff disputes actions or attempts unauthorized override of rejection rule. | Audit-log all approve/reject actions; enforce “any member rejected ⇒ entire policy rejected”; prohibit overrides. | FR-13, FR-26, FR-28 |
| Reject Reasons – UC-07 | Rejection reason data | T, R | Staff enters inconsistent reasons or repudiates rejection. | Standardized rejection reasons list with optional remarks; audit logging of rejection events. | FR-14, FR-28 |
| Policy Issuance & Credentials – UC-07/03 | Credentials + policy PDF | S, I | Credential spoofing or interception of issued policy documents. | Username must be user-defined and be email/mobile; deliver policy docs via email only; secure email templates; enforce TLS for transport. | FR-15, FR-16; NFR-SEC-03 |
| Policy Holder Portal (Read-only) – UC-09/10/13 | Customer personal/policy/claim data | E, T | Policy holder modifies records or escalates privileges to edit data. | Enforce read-only access for policy holder; server-side authorization checks on all update APIs. | FR-17 |
| Claims Submission via Agent – UC-11 | Claims + supporting documents | T, D | Excessive claims submission (DoS/business abuse) or invalid claims for non-network hospitals. | Limit 3 claims per policy year; enforce network hospital-only rule (Phase 1); scanned copies accepted; validate required documents. | FR-18, FR-20, FR-21 |
| Claims for Dependents – UC-11 | Dependent claim attribution | T | Claim submitted against wrong member or outside policy context. | Select member (primary/dependent) on claim; route claim under same policy; validate member-policy linkage. | FR-19 |
| Process Claim – UC-12 | Claim decision + payment details | T, R | Tampering with cheque details or repudiation of approval/rejection. | Audit-log approve/reject actions; store cheque details with integrity controls; enforce SLA tracking (7 working days). | FR-22, FR-23, FR-28 |
| Agent Policy Access – UC-08 | Agent portfolio data | I, E | Agent accesses other agents’ policies (data leakage / privilege escalation). | Restrict agent views to own policies only; enforce RBAC + row-level security/filters. | FR-24 |
| Edit After Submission – UC-05/06 | Submitted application integrity | T | Post-submission tampering with application data to influence approval. | Disable editing after submission; reject update API calls for submitted applications. | FR-25 |
| Status Notifications – UC-07/12 | Notification channel + content | I, S | Sensitive data leakage via notifications; spoofed notification messages. | Send status change notifications via email & SMS; include minimal PHI; sign emails (SPF/DKIM/DMARC) and use approved SMS gateway; avoid embedding attachments in SMS. | FR-29 |

---

## B) Non-Functional Requirements

### B1. Requirements Traceability Matrix (RTM) - Non-Functional

| NFR ID | Requirement Area | Confirmed NFR (Consolidated) | Verification | Stakeholder | Status |
|---|---|---|---|---|---|
| NFR-OPS-01 | Availability | 99.5% monthly uptime. | Ops review + DR/monitoring test | IT Ops / Business | Confirmed |
| NFR-OPS-02 | Monitoring | Centralized logs + alerts for errors, latency, lockouts. | Ops review + DR/monitoring test | IT Ops | Confirmed |
| NFR-PERF-01 | Response Time | p95 ≤ 2s for login, dashboard, lists under normal load.; 200 concurrent users; 20 TPS peak for portal actions.; Same SLA for both (Phase 1). | Performance test / load test | Business / IT Ops, IT Ops | Confirmed |
| NFR-PERF-02 | Concurrency | 500 concurrent sessions combined.; < 1% HTTP error rate during peak tests.; Login, policy status list, claims list, approval queues. | Performance test / load test | Business / QA, IT Ops, IT Ops / QA | Confirmed |
| NFR-SEC-01 | Account Lockout | Yes, lockout applies to all login types.; Wrong password and unknown username both count.; Per username only (Phase 1).; Successful login resets it; password reset does not.; Permanent until manual reactivation.; Yes, show remaining attempts to the user.; Email to user and security mailbox only. | Security test + review | Security, Security / Business, Security / Operations, UX / Security | Confirmed |
| NFR-SEC-02 | Manual Reactivation | Only System Admin role in intranet admin console.; Verify identity via registered email + admin approval.; Yes, record userId, adminId, timestamp, reason.; Yes, force password change at next login. | Security test + review | Compliance, Operations / Compliance, Security, Security / Compliance | Confirmed |
| NFR-SEC-03 | Data Security | TLS 1.2+ in transit; AES-256 at rest. | Security test + review | Security | Confirmed |
| NFR-SEC-04 | Privacy | Retain 7 years; purge after retention window. | Security test + review | Legal / Compliance | Confirmed |
| NFR-UX-01 | Common Header | Logo + portal name + logged-in user + logout.; Same design; portal label differs (PH/Agent/Company). | Review / test | UX, UX / Business | Confirmed |
| NFR-UX-02 | Common Footer | Copyright + privacy + terms + version. | Review / test | Business / Legal | Confirmed |
| NFR-UX-03 | Left Navigation | Yes, show only permitted items for the role.; Highlight current menu; breadcrumbs optional. | Review / test | Security / UX, UX | Confirmed |
| NFR-UX-04 | Minimum Clicks | ≤ 2 clicks from home to any primary screen.; Yes, deep links allowed; enforce authorization. | Review / test | Business / UX, Security / Architecture | Confirmed |
| NFR-UX-05 | Ease of Use | Yes, consistent layout and navigation pattern. | Review / test | UX | Confirmed |

### B2. STRIDE Threat-to-Control Mapping - Non-Functional

| NFR Use Case | Asset | STRIDE | Threat | Controls | Verification |
|---|---|---|---|---|---|
| NFR-SEC-01 Account Lockout | Auth subsystem | S, D | Brute force / credential stuffing; account takeover; auth endpoint DoS. | Lock after 3 consecutive invalid attempts; includes unknown usernames; per-username scope; reset counter on success; permanent lock until manual reactivation; show remaining attempts; email notifications to user & security mailbox. | Security test: brute-force simulation; verify lock, messaging, and notification behavior. |
| NFR-SEC-02 Manual Reactivation | Account lifecycle + admin console | E, R | Unauthorized unlock (privilege escalation) or repudiation of admin actions. | Only System Admin role can unlock; verify identity via registered email + admin approval; audit log unlock actions; force password change at next login. | Access review + audit log validation + negative tests for non-admin roles. |
| NFR-SEC-03 Encryption | PII/PHI + documents | I, T | Eavesdropping or data theft; tampering of stored files. | TLS 1.2+ in transit; AES-256 at rest; integrity checks for stored documents (hashing). | Security review: TLS configuration scan; storage encryption validation; penetration testing. |
| NFR-SEC-04 Retention & Purge | Claims/documents storage | I, R | Over-retention causing privacy exposure; inability to prove deletion/retention compliance. | Retain 7 years; purge after retention window; audit logs for purge jobs. | Compliance review + job run evidence + sample purge tests. |
| NFR-UX-03 Role-based Navigation | UI navigation + authorization boundaries | E | Users discover hidden links / unauthorized screens via direct URLs (elevation). | Role-based nav hides unauthorized items; deep links allowed but enforce authorization checks server-side. | Authorization tests using direct URL access for unauthorized roles. |
| NFR-PERF-01 Response Time | Portal responsiveness | D | Slow responses under load leading to availability impact. | p95 ≤ 2s for login/dashboard/lists under normal load (200 concurrent; 20 TPS); same SLA for intranet and internet. | Performance test measuring p95 response time. |
| NFR-PERF-02 Concurrency | System capacity | D | Resource exhaustion from spikes (DoS). | Support 500 concurrent sessions; maintain <1% HTTP error rate; focus on critical transactions (login, lists, approval queues). | Load test at 500 sessions; verify error-rate threshold and critical transactions. |
| NFR-OPS-01 Availability | Service uptime | D | Outages reduce availability for claims/policy operations. | 99.5% monthly uptime target; implement redundancy and monitored deployments. | Ops SLA reporting and incident postmortems. |
| NFR-OPS-02 Monitoring & Alerting | Telemetry, logs, alerts | R, D | Lack of observability enables repudiation and delays detection of attacks/outages. | Centralized logs + alerts for errors, latency, lockouts; retain logs per policy. | Monitoring test: generate errors/lockouts and confirm alerts and dashboards. |

---

## Sources
- Stakeholder clarification table: `AA_RequirementClarification.docx` (Confirmed items only).
- Functional use cases: `IMIC_Functional_Use_Cases.md` (repo link provided by requester).
- NFR use cases: `IMIC_NFR_Use_Cases.md` (repo link provided by requester).

## Notes
- STRIDE mappings describe common threat scenarios and suggested controls aligned to confirmed requirements. Extend with project-specific security standards (e.g., MFA, WAF, DLP) during detailed design.