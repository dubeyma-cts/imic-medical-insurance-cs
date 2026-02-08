# C4 Model - System Context, Container, Component & Deployment (IMIC)

This document provides **C4 System Context (L1)**, **C4 Container (L2)**, **C4 Component (L3)**, and **C4 Deployment (L4)** views for the IMIC platform.
It embeds **security controls** and **data flows** directly into the diagrams.

## 1. Scope & Inputs
- IMIC supports **Policy Holder** and **Agent** via an internet portal and **Company Staff** via an intranet portal. citeturn1search1
- Core behaviors include policy purchase via agent, company approval/rejection, and claim lifecycle (submission via agent, processing by company). citeturn1search1
- The repository describes a **microservices-based architecture**, a **gateway**, and **separate databases per service**, plus observability and security capabilities. citeturn14search49

> Note: Deployment details below are a **reference deployment** consistent with the repo’s cloud-ready posture and typical enterprise patterns (gateway, services, observability). citeturn14search49

---

## 2. C4 - System Context (Level 1) with Security + Data Flows

```mermaid
flowchart LR
  %% Actors
  PH["Policy Holder"]
  AG["Insurance Agent"]
  CS["Company Staff"]
  SA["System Admin"]

  %% External Systems
  IDP["Identity Provider\n(OIDC/OAuth2)"]
  MSG["Email/SMS Gateway"]
  DOCV["Document Validation\n(Automated checks)"]
  BANK["Cheque/Bank Processing\n(Phase 1)"]
  OBS["Monitoring & Logging\n(centralized)"]

  %% System
  subgraph IMIC["IMIC Medical Insurance Platform"]
    SYS["IMIC System\nSecurity: TLS + RBAC + Audit\nData: Policy, Claims, Documents"]
  end

  %% Relationships (include protocol + data)
  PH -->|"HTTPS/TLS\nAuth + Read-only Views\nData: Policy/Claims status"| SYS
  AG -->|"HTTPS/TLS\nCreate/Submit\nData: Policy Application + Docs\nData: Claim Request + Docs"| SYS
  CS -->|"HTTPS/TLS (Intranet)\nApprove/Reject\nData: Decisions + Reasons"| SYS
  SA -->|"HTTPS/TLS (Intranet)\nUnlock/Config\nData: Admin actions"| SYS

  SYS -->|"OIDC/OAuth2\nJWT/Claims"| IDP
  SYS -->|"Email/SMS\nData: Status/Alerts"| MSG
  SYS -->|"Validation Request\nData: Document metadata"| DOCV
  SYS -->|"Cheque Details\nData: Payment/Disbursement"| BANK
  SYS -->|"Logs/Metrics/Traces\nData: Telemetry"| OBS
```

---

## 3. C4 - Container (Level 2) with Security + Data Flows

```mermaid
flowchart TB
  %% Users
  PH["Policy Holder\n(Web Browser)"]
  AG["Insurance Agent\n(Web Browser)"]
  CS["Company Staff\n(Intranet Browser)"]
  SA["System Admin\n(Intranet Browser)"]

  %% External
  IDP["Identity Provider\n(OIDC/OAuth2)"]
  MSG["Email/SMS Gateway"]
  DOCV["Document Validation Service"]
  BANK["Cheque/Bank Processing"]
  OBS["Centralized Observability\nLogs/Metrics/Traces"]

  %% IMIC Containers
  subgraph IMIC["IMIC Platform"]

    subgraph UI["Presentation"]
      WEB["Internet Web Portal\n(Policy Holder + Agent)\nSecurity: HTTPS/TLS"]
      INTRA["Intranet Portal\n(Company Staff + Admin)\nSecurity: HTTPS/TLS"]
    end

    GW["API Gateway\nSecurity: JWT validation + Rate limiting\nCorrelation-Id"]

    subgraph SVC["Domain Services (Microservices)"]
      AUTH["Identity & Access Service\nLockout + Manual unlock\nRBAC/Claims"]
      POL["Policy Service\nPolicy applications + Members\nPremium calc"]
      CLM["Claims Service\nClaims lifecycle + SLA tracking"]
      PAY["Payments Service\nCheque capture + payout"]
      DOC["Document Service\nUpload/store/retrieve\nPDF/JPG allowlist"]
      NOTIF["Notification Service\nEmail/SMS dispatch"]
      AUD["Audit Service\nImmutable audit trail"]
    end

    subgraph DATA["Data Stores (Encrypted at Rest)"]
      IDDB["Identity DB"]
      POLDB["Policy DB"]
      CLMDB["Claims DB"]
      PAYDB["Payments DB"]
      DOCSTORE["Document Store\n(Encrypted)"]
      AUDDB["Audit Log Store\n(Write-optimized)"]
    end

  end

  %% User -> UI
  PH --> WEB
  AG --> WEB
  CS --> INTRA
  SA --> INTRA

  %% UI -> Gateway
  WEB -->|"HTTPS/TLS\nData: JSON/Files"| GW
  INTRA -->|"HTTPS/TLS\nData: JSON"| GW

  %% Gateway -> Services (annotate main data)
  GW -->|"JWT+RBAC\nAuth"| AUTH
  GW -->|"JWT+RBAC\nData: PolicyApplication"| POL
  GW -->|"JWT+RBAC\nData: ClaimRequest"| CLM
  GW -->|"JWT+RBAC\nData: ChequeDetails"| PAY
  GW -->|"JWT+RBAC\nData: PDF/JPG docs"| DOC
  GW -->|"JWT+RBAC\nData: Messages"| NOTIF
  GW -->|"JWT+RBAC\nData: AuditEvents"| AUD

  %% Service -> Data
  AUTH --> IDDB
  POL --> POLDB
  CLM --> CLMDB
  PAY --> PAYDB
  DOC --> DOCSTORE
  AUD --> AUDDB

  %% Services -> External
  AUTH -->|"OIDC/OAuth2"| IDP
  NOTIF -->|"Email/SMS"| MSG
  DOC -->|"Metadata + hashes"| DOCV
  PAY -->|"Cheque processing"| BANK

  %% Telemetry
  WEB -.-> OBS
  INTRA -.-> OBS
  GW -.-> OBS
  AUTH -.-> OBS
  POL -.-> OBS
  CLM -.-> OBS
  PAY -.-> OBS
  DOC -.-> OBS
  NOTIF -.-> OBS
  AUD -.-> OBS
```

---

## 4. C4 - Component (Level 3)

### 4.1 Policy Service - Component Diagram (with Security + Data Flow)

```mermaid
flowchart TB
  GW["API Gateway\nJWT validation + Rate limiting"]

  AUTH["Identity & Access Service\nRBAC/Claims"]
  DOC["Document Service\nPDF/JPG allowlist"]
  PAY["Payments Service\nCheque capture"]
  AUD["Audit Service\nImmutable trail"]
  NOTIF["Notification Service\nEmail/SMS"]

  POLDB["Policy DB\nEncrypted"]

  subgraph POLSVC["Policy Service (Container)"]
    API["Policy API\nAuthorize() + Input validation"]
    VAL["Policy Validator\nConstraints: active-policy, dependents<=5, relationship allowlist, cover range"]
    PREM["Premium Calculator\nConfig-driven\nServer-side calc"]
    WF["Submission Workflow\nDocs complete + cheque at submit\nFreeze after submit"]
    STATE["Policy Status Manager\nDraft->Submitted->Active/Rejected"]
    REPO["Policy Repository\nTransactions + optimistic concurrency"]
    OUTBOX["Outbox/Event Publisher\nIdempotent events"]
  end

  GW -->|"HTTPS/TLS\nPolicyApplication"| API
  API -->|"Claims/Role"| AUTH
  API --> VAL
  API --> PREM
  API --> WF

  VAL --> REPO
  PREM --> REPO
  WF --> STATE
  STATE --> REPO

  REPO --> POLDB

  WF -->|"Doc completeness check"| DOC
  WF -->|"Cheque capture"| PAY

  STATE -->|"AuditEvent (Approve/Reject)"| AUD
  OUTBOX -->|"Status change"| NOTIF
```

### 4.2 Claims Service - Component Diagram (with Security + Data Flow)

```mermaid
flowchart TB
  GW["API Gateway\nJWT validation + Rate limiting"]

  AUTH["Identity & Access Service\nRBAC/Claims"]
  POL["Policy Service\nEligibility + Member linkage"]
  DOC["Document Service\nPDF/JPG allowlist"]
  PAY["Payments Service\nCheque payout"]
  AUD["Audit Service\nImmutable trail"]
  NOTIF["Notification Service\nEmail/SMS"]

  CLMDB["Claims DB\nEncrypted"]

  subgraph CLMSVC["Claims Service (Container)"]
    API["Claims API\nAuthorize() + Input validation"]
    RULES["Claim Rules/Validator\n- 3 claims/policy-year\n- Network hospital (Phase 1)\n- Required docs present"]
    SLA["SLA Tracker\n7 working days target"]
    WF["Claims Workflow\nSentForApproval->Accepted/Rejected"]
    STATE["Claim Status Manager\nStatus transitions"]
    REPO["Claims Repository\nTransactions + optimistic concurrency"]
    OUTBOX["Outbox/Event Publisher\nIdempotent events"]
  end

  GW -->|"HTTPS/TLS\nClaimRequest"| API
  API -->|"Claims/Role"| AUTH

  API --> RULES
  API --> SLA
  API --> WF

  RULES -->|"Verify policy + member"| POL
  RULES -->|"Check docs"| DOC

  WF --> STATE
  STATE --> REPO
  SLA --> REPO
  REPO --> CLMDB

  STATE -->|"AuditEvent (Approve/Reject)"| AUD
  STATE -->|"Accepted->Cheque details"| PAY
  OUTBOX -->|"Status change"| NOTIF
```

---

## 5. C4 - Deployment (Level 4) with Security + Data Flow

> Reference deployment aligned to cloud-ready microservices, API gateway, observability, and security posture described in the repository. citeturn14search49

```mermaid
flowchart TB
  %% Client side
  subgraph Client["Client Devices"]
    PHC["Policy Holder Browser"]
    AGC["Agent Browser"]
    CSC["Company Staff Intranet Browser"]
    SAC["Admin Intranet Browser"]
  end

  %% Edge
  subgraph Edge["Edge / Perimeter"]
    DNS["DNS"]
    WAF["WAF / DDoS Protection"]
    CDN["CDN (static assets)"]
  end

  %% Cloud environment
  subgraph Cloud["Cloud Environment"]

    subgraph Net["Network"]
      VNET["VNet/Subnets"]
      FW["Firewall / NSG Rules"]
    end

    subgraph App["Application Tier"]
      WEBAPP["Web Portal Hosting\n(Internet Portal)"]
      INTRAAPP["Intranet Portal Hosting\n(Intranet Portal)"]
      APIGW["API Gateway\n(YARP/Ocelot)"]
      AUTH["Identity & Access Service"]
      POL["Policy Service"]
      CLM["Claims Service"]
      PAY["Payments Service"]
      DOC["Document Service"]
      NOTIF["Notification Service"]
      AUD["Audit Service"]
    end

    subgraph Data["Data Tier (Encrypted)"]
      IDDB["Identity DB"]
      POLDB["Policy DB"]
      CLMDB["Claims DB"]
      PAYDB["Payments DB"]
      DOCSTORE["Document Store"]
      AUDDB["Audit Log Store"]
    end

    subgraph Obs["Observability"]
      LOGS["Central Logs"]
      METRICS["Metrics"]
      TRACES["Traces"]
    end

  end

  %% External systems
  IDP["Identity Provider\n(OIDC/OAuth2)"]
  MSG["Email/SMS Gateway"]
  DOCV["Document Validation"]
  BANK["Cheque/Bank Processing"]

  %% Flows (include protocol + data)
  PHC --> DNS --> WAF --> CDN --> WEBAPP
  AGC --> DNS --> WAF --> CDN --> WEBAPP
  CSC --> DNS --> WAF --> INTRAAPP
  SAC --> DNS --> WAF --> INTRAAPP

  WEBAPP -->|"HTTPS/TLS\nJSON/Files"| APIGW
  INTRAAPP -->|"HTTPS/TLS\nJSON"| APIGW

  APIGW -->|"JWT validated\nPolicyApplication"| POL
  APIGW -->|"JWT validated\nClaimRequest"| CLM
  APIGW -->|"JWT validated\nAuth/Admin"| AUTH
  APIGW -->|"JWT validated\nDocs"| DOC
  APIGW -->|"JWT validated\nPayments"| PAY
  APIGW -->|"JWT validated\nAuditEvents"| AUD
  APIGW -->|"JWT validated\nMessages"| NOTIF

  AUTH --> IDDB
  POL --> POLDB
  CLM --> CLMDB
  PAY --> PAYDB
  DOC --> DOCSTORE
  AUD --> AUDDB

  AUTH -->|"OIDC/OAuth2"| IDP
  NOTIF -->|"Email/SMS"| MSG
  DOC -->|"Metadata"| DOCV
  PAY -->|"Cheque"| BANK

  %% Telemetry
  WEBAPP -.-> LOGS
  INTRAAPP -.-> LOGS
  APIGW -.-> LOGS
  AUTH -.-> LOGS
  POL -.-> LOGS
  CLM -.-> LOGS
  PAY -.-> LOGS
  DOC -.-> LOGS
  NOTIF -.-> LOGS
  AUD -.-> LOGS

  LOGS --> METRICS
  LOGS --> TRACES
```

### Deployment Security Controls (Minimum)
- **Perimeter**: WAF/DDoS and firewall rules; only required ports open.
- **Transport**: TLS for all ingress/egress.
- **Identity**: OIDC/OAuth2 with JWT/claims; lockout enforced by Identity & Access.
- **Data**: encryption at rest for DBs and document store.
- **Observability**: centralized logs/metrics/traces with alerts.

---

## 6. End-to-End Data Flows (Supplementary)

### 6.1 Policy Purchase & Approval - Data Flow

```mermaid
sequenceDiagram
  autonumber
  actor AG as Insurance Agent
  participant WEB as Web Portal
  participant GW as API Gateway
  participant POL as Policy Service
  participant DOC as Document Service
  participant PAY as Payments Service
  participant AUD as Audit Service
  participant NOTIF as Notification Service
  actor CS as Company Staff

  AG->>WEB: Enter policy + members + upload docs
  WEB->>GW: PolicyApplication + PDF/JPG (TLS)
  GW->>POL: Create/Update draft (JWT+RBAC)
  POL->>DOC: Store documents + metadata
  POL->>POL: Calculate premium (server-side)
  WEB->>GW: Submit application
  GW->>POL: Submit (idempotent)
  POL->>PAY: Capture cheque details (Phase 1)

  CS->>GW: Review application
  GW->>POL: Fetch details
  GW->>DOC: Fetch docs
  CS->>GW: Approve/Reject + reason
  GW->>POL: Decision
  POL->>AUD: Write audit event
  POL->>NOTIF: Notify status change
```

### 6.2 Claim Submission & Processing - Data Flow

```mermaid
sequenceDiagram
  autonumber
  actor PH as Policy Holder
  actor AG as Insurance Agent
  participant WEB as Web Portal
  participant GW as API Gateway
  participant CLM as Claims Service
  participant POL as Policy Service
  participant DOC as Document Service
  participant PAY as Payments Service
  participant AUD as Audit Service
  participant NOTIF as Notification Service
  actor CS as Company Staff

  PH->>AG: Provide claim request + bills + reports
  AG->>WEB: Enter claim + upload docs
  WEB->>GW: ClaimRequest + PDF/JPG (TLS)
  GW->>CLM: Submit claim (JWT+RBAC)
  CLM->>POL: Verify policy/member eligibility
  CLM->>DOC: Store documents + metadata
  CLM->>CLM: Apply rules (limit/network hospital)

  CS->>GW: Review claim
  GW->>CLM: Fetch claim
  CS->>GW: Accept/Reject
  GW->>CLM: Decision
  CLM->>AUD: Write audit event
  alt Accepted
    CLM->>PAY: Record cheque payout details
  end
  CLM->>NOTIF: Notify status change
```

---

## 7. References (Project Artifacts)
- Functional use cases: `docs/architecture/ArchitectureRequirementAnalysisDesign/IMIC_Functional_Use_Cases.md` citeturn1search1
- NFR use cases: `docs/architecture/ArchitectureRequirementAnalysisDesign/IMIC_NFR_Use_Cases.md` citeturn1search1
- RTM + STRIDE: `docs/architecture/ArchitectureRequirementAnalysisDesign/IMIC_RTM_STRIDE_Functional_NFR_GitHub.md` citeturn11search1
- Repo architecture overview (microservices/gateway/observability/security): repository README citeturn14search49
