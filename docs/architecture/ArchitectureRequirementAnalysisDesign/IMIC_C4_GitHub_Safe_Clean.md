# IMIC - C4 Diagrams (Context, Container, Component, Deployment)

This document is a **GitHub-safe** version of the IMIC C4 diagrams.
It avoids Mermaid edge cases (reserved words, unsupported note syntax in flowcharts, and risky labels) and is intended to render cleanly on GitHub.

> Tip: GitHub can render Mermaid diagrams inside fenced code blocks (` ```mermaid `). You can also check the Mermaid version GitHub uses by running ` ```mermaid\ninfo\n``` ` in a Markdown file. 

---

## 1) C4 System Context (L1)

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

## 2) C4 Container (L2)

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
 AUTH["Identity and Access Service\nLockout, Manual unlock\nRBAC"]
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

 WEB -->|"HTTPS TLS"| GW
 INTRA -->|"HTTPS TLS"| GW

 GW -->|"JWT RBAC"| AUTH
 GW -->|"JWT RBAC"| POL
 GW -->|"JWT RBAC"| CLM
 GW -->|"JWT RBAC"| PAY
 GW -->|"JWT RBAC"| DOC
 GW -->|"JWT RBAC"| NOTIF
 GW -->|"JWT RBAC"| AUD

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

## 3) C4 Component (L3) - Policy Service

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

 GW -->|"HTTPS TLS"| API
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

---

## 4) C4 Component (L3) - Claims Service

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

 GW -->|"HTTPS TLS"| API
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

## 5) C4 Deployment (L4) - Reference Deployment

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

## 6) Deployment Variants (VM vs Kubernetes vs App Service)

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

## 7) Why GitHub Rendering Can Fail (Quick Checklist)

1. Ensure every diagram is inside a fenced block that starts with ` ```mermaid ` and ends with ` ``` `. 
2. Validate Mermaid flowcharts for reserved words like `end` (lowercase can break flowcharts). 
3. Avoid putting `note over ...` inside `flowchart` diagrams (notes belong to sequence diagrams). Use comments `%%` inside flowcharts instead. 
4. If you use third-party Mermaid browser plugins, they can conflict with GitHub rendering. 

---

## 8) Next Step
If you paste the **exact GitHub error text** (it usually includes “Parse error on line ...”), I can pinpoint the specific Mermaid block and provide a minimal patch.


## References
- GitHub Docs: Creating diagrams (Mermaid fenced blocks).
- Mermaid Docs: Flowchart syntax and reserved words.
