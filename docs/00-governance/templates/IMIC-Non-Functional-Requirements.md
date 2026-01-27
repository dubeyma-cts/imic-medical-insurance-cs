
# IMIC – Non‑Functional Requirements (NFR)
**Document ID:** IMIC-NFR-001  
**Version:** 1.0  
**Date:** 2026-01-27

## 1. Purpose & Scope
This document defines the non‑functional requirements (NFRs) and service objectives for the IMIC platform built on .NET. It covers the member Web Portal, API Gateway, and backend services: Policy, Claims, Payments, and Members. Targets apply to **Production** unless stated; **Staging** should meet or exceed these prior to release.

## 2. Assumptions
- Azure hosting (App Service/AKS), managed DB (Azure SQL or PostgreSQL), Redis cache, and Azure Service Bus/Kafka.
- Identity via Azure Entra ID (OIDC). TLS enforced end‑to‑end.
- Each service owns its database; asynchronous events for cross‑service communication.

## 3. Service Level Objectives (SLOs)
### 3.1 Availability
- **Platform-wide monthly availability:** ≥ **99.9%**.
- **Critical APIs (Auth, Payments callback, Policy issue/renew):** ≥ **99.95%**.
- **Maintenance windows:** ≤ 2 hours/month; zero-downtime preferred with blue/green or rolling.

### 3.2 Reliability & Resilience
- **Error budget:** ≤ 0.1% of requests failing (5xx/timeout) per rolling 30 days.
- **Timeouts:** Service-to-service HTTP calls default **2–5s** with exponential backoff.
- **Circuit breakers:** Open on P95 latency > 2x baseline for 1 minute or failure rate > 5%.
- **Idempotency:** All POSTs that can retry must accept idempotency keys.

### 3.3 Performance & Latency (95th percentile)
| Area | Read (GET) | Write (POST/PUT) | Notes |
|---|---:|---:|---|
| Web Portal page loads | ≤ 2.5s TTI | n/a | over 4G/20Mbps typical | 
| API Gateway routing overhead | ≤ 20ms | ≤ 20ms | excluding backend | 
| Policy Service | ≤ 300ms | ≤ 600ms | create/endorse/renew |
| Claims Service | ≤ 350ms | ≤ 800ms | adjudication async beyond SLA |
| Payments Service | ≤ 250ms | ≤ 400ms | excludes external gateway time |
| Members Service | ≤ 300ms | ≤ 500ms | KYC calls may be async |

### 3.4 Throughput & Capacity
- **Steady state:** 300 RPS aggregate, with spiky traffic to **1,000 RPS** for 10 minutes.
- **Background jobs:** Able to process **50k** events/hour with at-least-once semantics.
- **Documents:** Support uploads up to **10 MB** per file; total claim docs ≤ **50 MB** per claim.

### 3.5 Scalability
- Horizontal scale to **3× peak** within **10 minutes** (autoscale on CPU>60%, RPS, and queue depth).
- Stateless services; session state externalized to Redis.

### 3.6 Observability
- **Tracing:** OpenTelemetry with W3C trace context propagated across all services.
- **Metrics:** RED + USE (Requests, Errors, Duration; Utilization, Saturation, Errors) per service.
- **Logging:** Structured, PII/PHI masked; correlation id on every log line.
- **Dashboards:** Per service with SLO and burn-rate alerts.

## 4. Security & Privacy
- **Authentication:** OIDC/OAuth2 (Entra ID), short‑lived access tokens, refresh via standard flows.
- **Authorization:** Role & claim-based; least privilege; deny by default.
- **Transport Security:** TLS 1.2+; HSTS on web; no mixed content.
- **Data Protection:** Encryption at rest (DB, storage) and in transit; Key Vault for secrets.
- **Input Validation:** Positive validation, size limits, anti-DTO over-posting.
- **Secrets:** No secrets in code/CI; managed identity preferred over client secrets.
- **Audit:** Immutable audit for policy issue/renew/endorse, claims decisions, payouts.
- **Vulnerability management:** No **Critical/High** issues outstanding at release; CodeQL and dependency scanning on main & PRs.

## 5. Compliance & Data Governance
- **Data classification:** PII/PHI identified; masked in logs and lower envs; synthetic data in non-prod.
- **Retention:** Policy/claims artifacts retained per regulatory guidance (e.g., 7–10 years); delete/archive processes defined.
- **Subject rights:** Provide export on request; support erasure where lawful.
- **Backup & Restore:** Daily full, 15‑minute PITR; **RPO ≤ 15 min**, **RTO ≤ 1 hour**.

## 6. Availability & DR Strategy
- **Zones:** Multi‑AZ for production workloads; DB in zone-redundant configuration.
- **DR:** Warm standby in paired region; failover tested twice per year; DNS TTL ≤ 300s.

## 7. Operability
- **Health endpoints:** `/health/live` and `/health/ready` per service; gateway aggregates.
- **Runbooks:** For common incidents (payment webhook backlog, queue poison messages, token failures).
- **Feature flags:** Toggle risky features; state persisted across restarts.
- **Configuration:** Hierarchical appsettings + environment overrides; secrets in Key Vault.

## 8. Accessibility & UX
- Web Portal meets **WCAG 2.1 AA** for core flows; keyboard navigation & ARIA roles implemented.
- Localization for English + at least one Indian language; Unicode-compatible fonts.

## 9. Interoperability & APIs
- **Contract‑first:** OpenAPI 3.0; breaking changes via versioned routes (e.g., `/v2`).
- **Pagination & Limits:** Default page size 50; max 200.
- **Idempotent payments:** Use payment reference + idempotency key; safe retries.
- **Webhooks:** Signed payloads; retry with exponential backoff; dead-letter strategy.

## 10. Testing & Quality Gates
- **Coverage:** ≥ 80% unit on critical domain; contract tests for all external APIs.
- **Performance tests:** Load/stress tests before each release; baseline stored.
- **Security tests:** SAST (CodeQL), dependency scans, secret scanning; optional DAST in pre‑prod.

## 11. Environment Parity
- Staging mirrors production topology and configuration classes; data is anonymized/synthetic.

## 12. Change Management
- Any NFR-affecting change requires an ADR and update to this document.
- SLO changes reviewed by Architecture + SRE + Business owners.

## 13. Traceability Matrix (sample)
| NFR ID | Capability | Measure | Owner | Validation |
|---|---|---|---|---|
| NFR-AV-001 | Availability | ≥99.9% monthly | SRE | Uptime dashboard, SLIs |
| NFR-PR-002 | Read latency | P95 ≤300ms | API Owners | Synthetic tests |
| NFR-SE-003 | No high vulns | 0 at release | Security | CodeQL/Alerts |
| NFR-DR-004 | DR failover | ≤1h RTO / ≤15m RPO | Platform | DR runbook test |

## 14. Open Questions
- Final choice of DB (Azure SQL vs PostgreSQL) per ADR.
- Event bus choice (Service Bus vs Kafka) per ADR.

---
**Approval:**  
Architecture Owner: ____________________  
Security Owner: ____________________  
SRE Owner: ____________________
