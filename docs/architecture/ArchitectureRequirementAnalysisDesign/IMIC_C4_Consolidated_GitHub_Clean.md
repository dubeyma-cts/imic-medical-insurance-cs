# IMIC - Consolidated C4 Architecture Pack (Context, Container, Component, Deployment)

This is a **single, GitHub-compatible** consolidated document that combines the content from the following C4-related artifacts in the repository (and fixes common Mermaid/GitHub rendering issues):

- IMIC_C4_Context_Container_Component_Deployment_DataFlows.mdturn20search87
- IMIC_C4_Context_Container_Component_Deployment_DataFlows_Variants_DecisionMatrix.mdturn20search87
- IMIC_C4_Context_Container_Component_Deployment_DataFlows_Variants_DecisionMatrix_Tradeoffs.mdturn20search87
- IMIC_C4_GitHub_Safe_Clean.mdturn20search87
- IMIC_C4_System_Context_Container_Component.mdturn21search92
- IMIC_C4_System_Context_and_Container.mdturn21search92

> **Why a consolidated version?** GitHub renders Mermaid diagrams only if each `mermaid` fenced block parses successfully; a single syntax issue can stop a diagram from rendering.turn20search87turn20search88

---

## Table of Contents

- [1. Scope & Assumptions](#1-scope--assumptions)
- [2. C4 System Context (L1)](#2-c4-system-context-l1)
- [3. C4 Container (L2)](#3-c4-container-l2)
- [4. C4 Component (L3)](#4-c4-component-l3)
  - [4.1 Policy Service Components](#41-policy-service-components)
  - [4.2 Claims Service Components](#42-claims-service-components)
- [5. C4 Deployment (L4) - Reference](#5-c4-deployment-l4---reference)
- [6. Deployment Variants](#6-deployment-variants)
  - [6.1 Variant A - All-in-One VM](#61-variant-a---all-in-one-vm)
  - [6.2 Variant B - Kubernetes](#62-variant-b---kubernetes)
  - [6.3 Variant C - App Service / PaaS](#63-variant-c---app-service--paas)
- [7. Decision Matrix & Cost Comparison](#7-decision-matrix--cost-comparison)
- [8. Variant Trade-offs](#8-variant-trade-offs)
- [9. End-to-End Data Flows (Supplementary)](#9-end-to-end-data-flows-supplementary)
- [10. GitHub Mermaid Rendering Checklist](#10-github-mermaid-rendering-checklist)

---

## 1. Scope & Assumptions

- IMIC supports **Policy Holder** and **Agent** via an Internet portal and **Company Staff** via an Intranet portal.turn14search49
- Architecture direction is **cloud-ready**, **microservices-based**, with an **API gateway**, separate databases per service, and observability.turn14search49
- C4 levels follow the standard C4 model hierarchy: Context, Container, Component, Deployment.turn21search92

---

## 2. C4 System Context (L1)

```mermaid
flowchart LR
  PH["Policy Holder"]
  AG["Insurance Agent"]
  CS["Company Staff"]
  SA["System Admin"]

  IDP["Identity Provider\nOIDC OAuth2"]
  MSG["Email and SMS Gateway"]
  DOCV["Document Validation\nAutomated checks"]
  BANK["Cheque and Bank Processing\nPhase 1"]
  OBS["Monitoring and Logging\nCentralized"]

  subgraph IMIC["IMIC Medical Insurance Platform"]
    SYS["IMIC System\nSecurity: TLS, RBAC, Audit\nData: Policy, Claims, Documents"]
  end

  PH -->|"HTTPS TLS\nLogin and read-only views"| SYS
  AG -->|"HTTPS TLS\nCreate and submit\nPolicy and claims"| SYS
  CS -->|"HTTPS TLS (Intranet)\nApprove and reject"| SYS
  SA -->|"HTTPS TLS (Intranet)\nUnlock and admin"| SYS

  SYS -->|"OIDC OAuth2\nJWT"| IDP
  SYS -->|"Notifications"| MSG
  SYS -->|"Doc validation"| DOCV
  SYS -->|"Cheque processing"| BANK
  SYS -->|"Telemetry"| OBS
```

---

## 3. C4 Container (L2)

```mermaid
flowchart TB
  PH["Policy Holder\nBrowser"]
  AG["Insurance Agent\nBrowser"]
  CS["Company Staff\nIntranet Browser"]
  SA["System Admin\nIntranet Browser"]

  IDP["Identity Provider\nOIDC OAuth2"]
  MSG["Email and SMS Gateway"]
  DOCV["Document Validation"]
  BANK["Cheque and Bank Processing"]
  OBS["Observability\nLogs Metrics Traces"]

  subgraph IMIC["IMIC Platform"]

    subgraph UI["Presentation"]
      WEB["Internet Web Portal\nPolicy Holder and Agent\nTLS"]
      INTRA["Intranet Portal\nCompany Staff and Admin\nTLS"]
    end

    GW["API Gateway\nJWT validation, Rate limiting\nCorrelation Id"]

    subgraph SVC["Services"]
      AUTH["Identity and Access\nLockout, Manual unlock\nRBAC"]
      POL["Policy Service\nPolicies, Members\nPremium"]
      CLM["Claims Service\nClaims lifecycle\nSLA tracking"]
      PAY["Payments Service\nCheque capture and payout"]
      DOC["Document Service\nUpload store retrieve\nPDF JPG allowlist"]
      NOTIF["Notification Service\nEmail SMS dispatch"]
      AUD["Audit Service\nImmutable audit trail"]
    end

    subgraph DATA["Data Stores (Encrypted)"]
      IDDB["Identity DB"]
      POLDB["Policy DB"]
      CLMDB["Claims DB"]
      PAYDB["Payments DB"]
      DOCSTORE["Document Store"]
      AUDDB["Audit Log Store"]
    end

  end

  PH --> WEB
  AG --> WEB
  CS --> INTRA
  SA --> INTRA

  WEB -->|"HTTPS TLS\nData: JSON and files"| GW
  INTRA -->|"HTTPS TLS\nData: JSON"| GW

  GW -->|"JWT RBAC"| AUTH
  GW -->|"JWT RBAC\nData: PolicyApplication"| POL
  GW -->|"JWT RBAC\nData: ClaimRequest"| CLM
  GW -->|"JWT RBAC\nData: ChequeDetails"| PAY
  GW -->|"JWT RBAC\nData: PDF JPG"| DOC
  GW -->|"JWT RBAC\nData: Notifications"| NOTIF
  GW -->|"JWT RBAC\nData: AuditEvents"| AUD

  AUTH --> IDDB
  POL --> POLDB
  CLM --> CLMDB
  PAY --> PAYDB
  DOC --> DOCSTORE
  AUD --> AUDDB

  AUTH -->|"OIDC OAuth2"| IDP
  NOTIF --> MSG
  DOC --> DOCV
  PAY --> BANK

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

## 4. C4 Component (L3)

### 4.1 Policy Service Components

```mermaid
flowchart TB
  GW["API Gateway\nJWT validation"]

  AUTH["Identity and Access\nRBAC"]
  DOC["Document Service\nPDF JPG allowlist"]
  PAY["Payments Service\nCheque capture"]
  AUD["Audit Service\nImmutable"]
  NOTIF["Notification Service\nEmail SMS"]

  POLDB["Policy DB\nEncrypted"]

  subgraph POLSVC["Policy Service"]
    API["Policy API\nAuthorize + Validate"]
    VAL["Policy Validator\nActive policy rule\nDependents limit\nRelationship allowlist\nCover range"]
    PREM["Premium Calculator\nConfig driven"]
    WF["Submission Workflow\nDocs complete\nCheque at submit\nFreeze after submit"]
    STATE["Policy Status Manager\nDraft Submitted Active Rejected"]
    REPO["Policy Repository\nTransactions\nOptimistic concurrency"]
    OUTBOX["Outbox Publisher\nIdempotent events"]
  end

  GW -->|"HTTPS TLS\nPolicyApplication"| API
  API --> AUTH
  API --> VAL
  API --> PREM
  API --> WF

  VAL --> REPO
  PREM --> REPO
  WF --> STATE
  STATE --> REPO

  REPO --> POLDB

  WF --> DOC
  WF --> PAY

  STATE --> AUD
  OUTBOX --> NOTIF
```

### 4.2 Claims Service Components

```mermaid
flowchart TB
  GW["API Gateway\nJWT validation"]

  AUTH["Identity and Access\nRBAC"]
  POL["Policy Service\nEligibility checks"]
  DOC["Document Service\nPDF JPG allowlist"]
  PAY["Payments Service\nCheque payout"]
  AUD["Audit Service\nImmutable"]
  NOTIF["Notification Service\nEmail SMS"]

  CLMDB["Claims DB\nEncrypted"]

  subgraph CLMSVC["Claims Service"]
    API["Claims API\nAuthorize + Validate"]
    RULES["Claim Rules\nClaim limit\nNetwork hospital\nDocs present"]
    SLA["SLA Tracker\n7 working days"]
    WF["Claims Workflow\nSentForApproval Accepted Rejected"]
    STATE["Status Manager\nTransitions"]
    REPO["Claims Repository\nTransactions\nOptimistic concurrency"]
    OUTBOX["Outbox Publisher\nIdempotent events"]
  end

  GW -->|"HTTPS TLS\nClaimRequest"| API
  API --> AUTH

  API --> RULES
  API --> SLA
  API --> WF

  RULES --> POL
  RULES --> DOC

  WF --> STATE
  STATE --> REPO
  SLA --> REPO
  REPO --> CLMDB

  STATE --> AUD
  STATE --> PAY
  OUTBOX --> NOTIF
```

---

## 5. C4 Deployment (L4) - Reference

```mermaid
flowchart TB
  subgraph Client["Client Devices"]
    PHC["Policy Holder Browser"]
    AGC["Agent Browser"]
    CSC["Company Staff Browser"]
  end

  subgraph Edge["Edge"]
    DNS["DNS"]
    WAF["WAF and DDoS"]
    CDN["CDN"]
  end

  subgraph Cloud["Cloud Environment"]
    WEBAPP["Web Portal Hosting"]
    INTRAAPP["Intranet Portal Hosting"]
    APIGW["API Gateway"]

    AUTH["Identity and Access Service"]
    POL["Policy Service"]
    CLM["Claims Service"]
    PAY["Payments Service"]
    DOC["Document Service"]
    NOTIF["Notification Service"]
    AUD["Audit Service"]

    IDDB["Identity DB"]
    POLDB["Policy DB"]
    CLMDB["Claims DB"]
    PAYDB["Payments DB"]
    DOCSTORE["Document Store"]
    AUDDB["Audit Log Store"]

    OBS["Observability Platform"]
  end

  IDP["Identity Provider\nOIDC OAuth2"]
  MSG["Email and SMS Gateway"]
  DOCV["Document Validation"]
  BANK["Cheque and Bank Processing"]

  PHC --> DNS --> WAF --> CDN --> WEBAPP
  AGC --> DNS --> WAF --> CDN --> WEBAPP
  CSC --> DNS --> WAF --> INTRAAPP

  WEBAPP -->|"HTTPS TLS"| APIGW
  INTRAAPP -->|"HTTPS TLS"| APIGW

  APIGW --> AUTH
  APIGW --> POL
  APIGW --> CLM
  APIGW --> PAY
  APIGW --> DOC
  APIGW --> NOTIF
  APIGW --> AUD

  AUTH --> IDDB
  POL --> POLDB
  CLM --> CLMDB
  PAY --> PAYDB
  DOC --> DOCSTORE
  AUD --> AUDDB

  AUTH --> IDP
  NOTIF --> MSG
  DOC --> DOCV
  PAY --> BANK

  WEBAPP -.-> OBS
  INTRAAPP -.-> OBS
  APIGW -.-> OBS
  AUTH -.-> OBS
  POL -.-> OBS
  CLM -.-> OBS
  PAY -.-> OBS
  DOC -.-> OBS
  NOTIF -.-> OBS
  AUD -.-> OBS
```

---

## 6. Deployment Variants

### 6.1 Variant A - All-in-One VM

```mermaid
flowchart TB
  PH["Policy Holder"]
  AG["Agent"]
  CS["Company Staff"]

  subgraph VM["Single VM Host"]
    WEB["Web Portal"]
    INTRA["Intranet Portal"]
    GW["API Gateway"]
    AUTH["Identity"]
    POL["Policy"]
    CLM["Claims"]
    PAY["Payments"]
    DOC["Documents"]
    NOTIF["Notifications"]
    AUD["Audit"]
  end

  subgraph Data["Managed Data"]
    DB["Databases and Stores\nEncrypted"]
  end

  PH --> WEB
  AG --> WEB
  CS --> INTRA

  WEB --> GW
  INTRA --> GW
  GW --> AUTH
  GW --> POL
  GW --> CLM
  GW --> PAY
  GW --> DOC
  GW --> NOTIF
  GW --> AUD

  AUTH --> DB
  POL --> DB
  CLM --> DB
  PAY --> DB
  DOC --> DB
  AUD --> DB
```

### 6.2 Variant B - Kubernetes

```mermaid
flowchart TB
  PH["Policy Holder"]
  AG["Agent"]
  CS["Company Staff"]

  subgraph Edge["Edge"]
    INGRESS["Ingress"]
  end

  subgraph K8S["Kubernetes Cluster"]
    WEB["Portal Pods"]
    GW["Gateway Pods"]
    SVC["Service Pods\nPolicy Claims Payments Docs Auth"]
  end

  subgraph Data["Managed Data"]
    DB["Databases and Stores\nEncrypted"]
  end

  PH --> INGRESS
  AG --> INGRESS
  CS --> INGRESS

  INGRESS --> WEB
  WEB --> GW
  GW --> SVC
  SVC --> DB
```

### 6.3 Variant C - App Service / PaaS

```mermaid
flowchart TB
  PH["Policy Holder"]
  AG["Agent"]
  CS["Company Staff"]

  subgraph PaaS["PaaS Runtime"]
    WEB["Web Apps\nPortals"]
    GW["Gateway App"]
    SVC["Service Apps\nPolicy Claims Payments Docs Auth"]
  end

  subgraph Data["Managed Data"]
    DB["Databases and Stores\nEncrypted"]
  end

  PH --> WEB
  AG --> WEB
  CS --> WEB
  WEB --> GW
  GW --> SVC
  SVC --> DB
```

---

## 7. Decision Matrix & Cost Comparison

### 7.1 Decision Matrix (Qualitative)

**Legend**: 1 = Low, 3 = Medium, 5 = High

| Criterion | All-in-One VM | Kubernetes | App Service / PaaS |
|---|---:|---:|---:|
| Time-to-market / setup speed | 5 | 3 | 4 |
| Operational complexity | 2 | 1 | 4 |
| Scalability (horizontal) | 2 | 5 | 4 |
| Resilience / self-healing | 2 | 5 | 4 |
| Deployment frequency support | 3 | 5 | 4 |
| Security isolation controls | 2 | 5 | 4 |
| Cost predictability | 4 | 3 | 4 |

### 7.2 Cost Comparison (Relative)

> Exact cost depends on cloud provider and sizing. This table is **relative**.

| Cost Dimension | All-in-One VM | Kubernetes | App Service / PaaS |
|---|---|---|---|
| Compute baseline | Low to Medium | Medium to High | Medium |
| Ops cost (people) | Low | High | Medium to Low |
| Scaling cost | Step-wise | Elastic, can be higher | Elastic, moderate |
| Availability cost (HA) | High when added | Medium | Medium |
| Typical total (relative) | $ | $$$ | $$ |

---

## 8. Variant Trade-offs

### 8.1 All-in-One VM
- Pros: simplest ops, fast PoC.
- Cons: limited elasticity, larger blast radius, HA increases complexity.

### 8.2 Kubernetes
- Pros: best elasticity, resilience, strongest isolation.
- Cons: highest platform complexity and baseline cost.

### 8.3 App Service / PaaS
- Pros: balanced ops, good scale, managed runtime.
- Cons: less control than Kubernetes, can cost more at sustained high scale.

---

## 9. End-to-End Data Flows (Supplementary)

### 9.1 Policy Purchase and Approval

```mermaid
sequenceDiagram
  autonumber
  actor AG as Agent
  participant WEB as Web Portal
  participant GW as API Gateway
  participant POL as Policy Service
  participant DOC as Document Service
  participant PAY as Payments Service
  participant AUD as Audit Service
  participant NOTIF as Notification Service
  actor CS as Company Staff

  AG->>WEB: Enter policy and members, upload docs
  WEB->>GW: PolicyApplication and PDF/JPG (TLS)
  GW->>POL: Create or update draft (JWT RBAC)
  POL->>DOC: Store docs and metadata
  POL->>POL: Calculate premium (server-side)
  WEB->>GW: Submit application
  GW->>POL: Submit (idempotent)
  POL->>PAY: Capture cheque details
  CS->>GW: Review and decide
  GW->>POL: Approve or reject
  POL->>AUD: Write audit event
  POL->>NOTIF: Send status notification
```

### 9.2 Claim Submission and Processing

```mermaid
sequenceDiagram
  autonumber
  actor PH as Policy Holder
  actor AG as Agent
  participant WEB as Web Portal
  participant GW as API Gateway
  participant CLM as Claims Service
  participant POL as Policy Service
  participant DOC as Document Service
  participant PAY as Payments Service
  participant AUD as Audit Service
  participant NOTIF as Notification Service
  actor CS as Company Staff

  PH->>AG: Provide claim request and documents
  AG->>WEB: Enter claim and upload docs
  WEB->>GW: ClaimRequest and PDF/JPG (TLS)
  GW->>CLM: Submit claim (JWT RBAC)
  CLM->>POL: Verify eligibility
  CLM->>DOC: Store docs and metadata
  CS->>GW: Review claim
  GW->>CLM: Accept or reject
  CLM->>AUD: Write audit event
  alt Accepted
    CLM->>PAY: Record cheque payout details
  end
  CLM->>NOTIF: Send status notification
```

---

## 10. GitHub Mermaid Rendering Checklist

1. Mermaid diagrams must be inside fenced blocks that start with ` ```mermaid ` and end with ` ``` `.turn20search87
2. Avoid reserved-word pitfalls in flowcharts (e.g., avoid lowercase node ids like `end`); use `END` or different ids.turn20search88
3. Do not use sequence-only syntax (e.g., `note over`) inside flowcharts; use `%%` comments instead.turn20search87
4. Third-party Mermaid browser plugins can conflict with GitHub rendering.turn20search87

---

## Sources
- GitHub Docs: Creating diagrams (Mermaid fenced blocks).turn20search87
- Mermaid Docs: Flowchart syntax and reserved words.turn20search88
- C4 Model overview.turn21search92
- Repository architecture overview (microservices, gateway, observability, security).turn14search49
