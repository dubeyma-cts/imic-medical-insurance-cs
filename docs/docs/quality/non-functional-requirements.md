# Non-Functional Requirements (summary)
- Availability: 99.9% monthly; critical flows 99.95%.
- Performance: P95 ≤ 300ms (reads), ≤ 600–800ms (writes) depending on service.
- Security: Lockout after 3 failures; TLS; RBAC; audit trails.
- DR: RTO ≤ 1h, RPO ≤ 15m.
