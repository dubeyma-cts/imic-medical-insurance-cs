# IMIC – Clarification Questions (Non-Functional Requirements)

The following questions clarify **non-functional requirements (NFRs)** for the IMIC system so that they are **measurable, testable, and implementable**.

| Use-Case ID | Requirement Area | Question | Response (Example) | Stakeholder | Status |
|---|---|---|---|---|---|
| NFR-SEC-01 | Account Lockout | Does the lockout apply to Policy Holder, Agent, and Company Staff logins? | Yes, lockout applies to all login types. | Security / Business | Open |
| NFR-SEC-01 | Account Lockout | What counts as an “invalid attempt” (wrong password only, unknown username, both)? | Wrong password and unknown username both count. | Security | Open |
| NFR-SEC-01 | Account Lockout | What is the lockout scope: per username, per IP, or both? | Per username only (Phase 1). | Security | Open |
| NFR-SEC-01 | Account Lockout | What resets the “consecutive invalid attempts” counter? | Successful login resets it; password reset does not. | Security | Open |
| NFR-SEC-01 | Account Lockout | Is the lockout permanent until admin unlock, or time-based? | Permanent until manual reactivation. | Security / Operations | Confirmed |
| NFR-SEC-01 | Account Lockout | Should the UI show remaining attempts (e.g., “2 attempts left”)? | Yes, show remaining attempts to the user. | UX / Security | Open |
| NFR-SEC-01 | Account Lockout | Should lock events trigger notifications (email/SMS) to user/admin? | Email to user and security mailbox only. | Security | Open |
| NFR-SEC-02 | Manual Reactivation | Who is authorized to unlock accounts (role/permission)? | Only System Admin role in intranet admin console. | Security / Compliance | Open |
| NFR-SEC-02 | Manual Reactivation | What verification is required before unlocking (KYC, agent validation, HR approval)? | Verify identity via registered email + admin approval. | Operations / Compliance | Open |
| NFR-SEC-02 | Manual Reactivation | Do we need audit logging for unlock actions (who/when/why)? | Yes, record userId, adminId, timestamp, reason. | Compliance | Open |
| NFR-SEC-02 | Manual Reactivation | After unlock, should the user be forced to change password? | Yes, force password change at next login. | Security | Open |
| NFR-UX-01 | Common Header | What elements must appear in the header across all pages (logo, user name, logout, help)? | Logo + portal name + logged-in user + logout. | UX / Business | Open |
| NFR-UX-01 | Common Header | Are there different headers per portal or exactly identical across all portals? | Same design; portal label differs (PH/Agent/Company). | UX | Open |
| NFR-UX-02 | Common Footer | What content should be in the footer (copyright, links, version)? | Copyright + privacy + terms + version. | Business / Legal | Open |
| NFR-UX-03 | Left Navigation | Should left navigation be role-based (hide unauthorized menu items)? | Yes, show only permitted items for the role. | Security / UX | Open |
| NFR-UX-03 | Left Navigation | Should the current page be highlighted and breadcrumbs shown? | Highlight current menu; breadcrumbs optional. | UX | Open |
| NFR-UX-04 | Minimum Clicks | Define “minimum mouse clicks”: what is the maximum clicks allowed from home to any primary screen? | ≤ 2 clicks from home to any primary screen. | Business / UX | Open |
| NFR-UX-04 | Minimum Clicks | Should deep links be supported (direct URL to a screen) with access control? | Yes, deep links allowed; enforce authorization. | Security / Architecture | Open |
| NFR-UX-05 | Ease of Use | Should the Agent and Policy Holder portals share the same layout grid and interaction patterns? | Yes, consistent layout and navigation pattern. | UX | Confirmed |
| NFR-UX-05 | Ease of Use | Are there accessibility requirements (WCAG level, keyboard navigation, contrast)? | WCAG 2.1 AA compliance required. | UX / Compliance | Open |
| NFR-PERF-01 | Response Time | What is the acceptable response time for key pages (p95 target)? | p95 ≤ 2s for login, dashboard, lists under normal load. | Business / IT Ops | Open |
| NFR-PERF-01 | Response Time | What is “normal load” definition (concurrent users, transactions/sec)? | 200 concurrent users; 20 TPS peak for portal actions. | IT Ops | Open |
| NFR-PERF-01 | Response Time | Are there separate SLAs for intranet vs internet portals? | Same SLA for both (Phase 1). | Business / IT Ops | Open |
| NFR-PERF-02 | Concurrency | What peak concurrency should the system support across PH/AG/CS? | 500 concurrent sessions combined. | IT Ops | Open |
| NFR-PERF-02 | Concurrency | What is the maximum acceptable error rate under load? | < 1% HTTP error rate during peak tests. | IT Ops / QA | Open |
| NFR-PERF-02 | Concurrency | Which transactions are considered “critical” for performance testing? | Login, policy status list, claims list, approval queues. | Business / QA | Open |
| NFR-OPS-01 | Availability | Is there an availability target (uptime) for the portals? | 99.5% monthly uptime. | IT Ops / Business | Open |
| NFR-OPS-02 | Monitoring | What monitoring/alerting is required (APM, logs, dashboards)? | Centralized logs + alerts for errors, latency, lockouts. | IT Ops | Open |
| NFR-SEC-03 | Data Security | Are there encryption requirements for data at rest and in transit? | TLS 1.2+ in transit; AES-256 at rest. | Security | Open |
| NFR-SEC-04 | Privacy | Are there data retention and purging rules for documents/claims? | Retain 7 years; purge after retention window. | Legal / Compliance | Open |

---

**Status Legend**
- **Open** – Clarification required from stakeholder
- **Confirmed** – Already agreed/explicit in requirements

> Tip: Use this table during NFR workshops to finalize measurable SLAs, UX consistency rules, and security controls.
