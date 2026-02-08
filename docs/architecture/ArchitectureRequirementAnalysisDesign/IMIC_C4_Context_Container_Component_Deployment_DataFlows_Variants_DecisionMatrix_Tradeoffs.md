---

## 8. Deployment Variants (Same C4 Semantics)

This section provides **three deployment variants** for the *same* logical containers (Portals, API Gateway, Microservices, Data Stores, Observability).
Choose the variant based on scale, ops maturity, and cost.

### 8.1 Variant A — All-in-One VM (Single Host + Managed DB)

**When to use**: PoC, training, small pilot, simplest ops.

```mermaid
flowchart TB
  subgraph Users["Users"]
    PH["Policy Holder Browser"]
    AG["Agent Browser"]
    CS["Company Staff Browser"]
  end

  subgraph VM["VM: IMIC All-in-One Host"]
    WEB["Web Portal (Internet)"]
    INTRA["Intranet Portal"]
    GW["API Gateway"]
    AUTH["Identity & Access Svc"]
    POL["Policy Svc"]
    CLM["Claims Svc"]
    PAY["Payments Svc"]
    DOC["Document Svc"]
    NOTIF["Notification Svc"]
    AUD["Audit Svc"]
  end

  subgraph Data["Managed Data Stores (Encrypted)"]
    IDDB["Identity DB"]
    POLDB["Policy DB"]
    CLMDB["Claims DB"]
    PAYDB["Payments DB"]
    DOCSTORE["Document Store"]
    AUDDB["Audit Log Store"]
  end

  subgraph Ext["External Systems"]
    IDP["OIDC/OAuth2 IdP"]
    MSG["Email/SMS Gateway"]
    DOCV["Doc Validation"]
    BANK["Cheque/Bank Processing"]
    OBS["Central Logs/Metrics"]
  end

  %% Edge + security
  PH -->|"HTTPS/TLS"| WEB
  AG -->|"HTTPS/TLS"| WEB
  CS -->|"HTTPS/TLS"| INTRA

  WEB -->|"HTTPS/TLS"| GW
  INTRA -->|"HTTPS/TLS"| GW

  GW -->|"JWT+RBAC"| AUTH
  GW -->|"JWT+RBAC"| POL
  GW -->|"JWT+RBAC"| CLM
  GW -->|"JWT+RBAC"| PAY
  GW -->|"JWT+RBAC"| DOC
  GW -->|"JWT+RBAC"| NOTIF
  GW -->|"JWT+RBAC"| AUD

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

  VM -.-> OBS
```

**Security controls**
- TLS everywhere; host hardening; OS patching; least-privilege service accounts.
- Gateway enforces JWT validation and rate limiting.
- Datastores encrypted at rest.

---

### 8.2 Variant B — Kubernetes (AKS/EKS/GKE) + Managed Data

**When to use**: scale-out, resilience, frequent releases, strong platform ops.

```mermaid
flowchart TB
  subgraph Users["Users"]
    PH["Policy Holder Browser"]
    AG["Agent Browser"]
    CS["Company Staff Browser"]
  end

  subgraph Edge["Edge"]
    DNS["DNS"]
    WAF["WAF/DDoS"]
    INGRESS["Ingress Controller"]
  end

  subgraph K8S["Kubernetes Cluster"]
    WEB["Web Portal Pods"]
    INTRA["Intranet Portal Pods"]
    GW["API Gateway Pods"]

    subgraph SVC["Microservice Pods"]
      AUTH["Identity & Access"]
      POL["Policy"]
      CLM["Claims"]
      PAY["Payments"]
      DOC["Document"]
      NOTIF["Notification"]
      AUD["Audit"]
    end

    OBSAG["Observability Agents\n(Logs/Metrics/Traces)"]
  end

  subgraph Data["Managed Data Stores (Encrypted)"]
    IDDB["Identity DB"]
    POLDB["Policy DB"]
    CLMDB["Claims DB"]
    PAYDB["Payments DB"]
    DOCSTORE["Document Store"]
    AUDDB["Audit Log Store"]
  end

  subgraph Ext["External Systems"]
    IDP["OIDC/OAuth2 IdP"]
    MSG["Email/SMS Gateway"]
    DOCV["Doc Validation"]
    BANK["Cheque/Bank Processing"]
    OBS["Central Observability Platform"]
  end

  PH --> DNS --> WAF --> INGRESS
  AG --> DNS --> WAF --> INGRESS
  CS --> DNS --> WAF --> INGRESS

  INGRESS -->|"HTTPS/TLS"| WEB
  INGRESS -->|"HTTPS/TLS"| INTRA

  WEB --> GW
  INTRA --> GW

  GW -->|"JWT+RBAC"| AUTH
  GW -->|"JWT+RBAC"| POL
  GW -->|"JWT+RBAC"| CLM
  GW -->|"JWT+RBAC"| PAY
  GW -->|"JWT+RBAC"| DOC
  GW -->|"JWT+RBAC"| NOTIF
  GW -->|"JWT+RBAC"| AUD

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

  OBSAG -.-> OBS
```

**Security controls**
- TLS ingress + mTLS service-to-service (recommended).
- Network policies between namespaces; pod security standards.
- Secrets in managed secret store; rotation.
- HPA for resilience; rate limiting at gateway/ingress.

---

### 8.3 Variant C — App Service / PaaS (Web Apps + Container Apps) + Managed Data

**When to use**: fast delivery with managed runtime; reduced cluster ops; moderate scale.

```mermaid
flowchart TB
  subgraph Users["Users"]
    PH["Policy Holder Browser"]
    AG["Agent Browser"]
    CS["Company Staff Browser"]
  end

  subgraph Edge["Edge"]
    DNS["DNS"]
    WAF["WAF/DDoS"]
    CDN["CDN"]
  end

  subgraph PaaS["PaaS Runtime"]
    WEB["Web App: Internet Portal"]
    INTRA["Web App: Intranet Portal"]
    GW["API Gateway App"]

    subgraph Apps["Service Apps"]
      AUTH["Identity & Access App"]
      POL["Policy App"]
      CLM["Claims App"]
      PAY["Payments App"]
      DOC["Document App"]
      NOTIF["Notification App"]
      AUD["Audit App"]
    end

    OBSAG["Managed Diagnostics/Agents"]
  end

  subgraph Data["Managed Data Stores (Encrypted)"]
    IDDB["Identity DB"]
    POLDB["Policy DB"]
    CLMDB["Claims DB"]
    PAYDB["Payments DB"]
    DOCSTORE["Document Store"]
    AUDDB["Audit Log Store"]
  end

  subgraph Ext["External Systems"]
    IDP["OIDC/OAuth2 IdP"]
    MSG["Email/SMS Gateway"]
    DOCV["Doc Validation"]
    BANK["Cheque/Bank Processing"]
    OBS["Central Observability Platform"]
  end

  PH --> DNS --> WAF --> CDN --> WEB
  AG --> DNS --> WAF --> CDN --> WEB
  CS --> DNS --> WAF --> INTRA

  WEB -->|"HTTPS/TLS"| GW
  INTRA -->|"HTTPS/TLS"| GW

  GW -->|"JWT+RBAC"| AUTH
  GW -->|"JWT+RBAC"| POL
  GW -->|"JWT+RBAC"| CLM
  GW -->|"JWT+RBAC"| PAY
  GW -->|"JWT+RBAC"| DOC
  GW -->|"JWT+RBAC"| NOTIF
  GW -->|"JWT+RBAC"| AUD

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

  OBSAG -.-> OBS
```

**Security controls**
- TLS termination at edge; private endpoints to data stores (recommended).
- Managed identity to access data/secret stores.
- Gateway rate limiting + WAF rules.

---

### 8.4 Variant Selection Guidance (Quick)
- **All-in-One VM**: lowest ops maturity, fastest PoC, least scalable.
- **Kubernetes**: best for high scale + resilience + frequent deployments, highest ops complexity.
- **App Service/PaaS**: balanced; strong managed capabilities with simpler ops than K8S.

---

## 9. Deployment Variant Decision Matrix (with Cost Comparison)

This matrix helps select between **All-in-One VM**, **Kubernetes**, and **App Service/PaaS** deployment variants while keeping the same C4 semantics (portals + gateway + services + data + observability).

### 9.1 Decision Matrix (Qualitative)

**Legend**: 1 = Poor/Low, 3 = Medium, 5 = Best/High

| Criterion | All-in-One VM | Kubernetes (AKS/EKS/GKE) | App Service / PaaS |
|---|---:|---:|---:|
| Time-to-market / setup speed | 5 | 3 | 4 |
| Operational complexity | 2 | 1 | 4 |
| Scalability (horizontal) | 2 | 5 | 4 |
| Resilience / self-healing | 2 | 5 | 4 |
| Deployment frequency support (CI/CD, blue-green) | 3 | 5 | 4 |
| Observability & diagnostics maturity | 3 | 5 | 4 |
| Security controls maturity (network segmentation, policy, mTLS) | 2 | 5 | 4 |
| Cost predictability | 4 | 3 | 4 |
| Best fit (Phase-1 IMIC training/pilot) | 5 | 3 | 4 |
| Best fit (Enterprise scale / long term) | 2 | 5 | 4 |

### 9.2 Cost Comparison (Relative, with Assumptions)

> **Important**: Exact cost depends heavily on cloud provider, region, sizing, traffic, storage, and HA targets. The comparison below is **relative** to guide decision-making.

**Shared cost drivers across all variants**
- Managed databases (Identity/Policy/Claims/Payments) and document storage.
- Observability (logs/metrics/traces ingestion and retention).
- Egress costs for email/SMS providers and any external integrations.

**Assumptions for comparison (example sizing)**
- Phase-1 target: ~200 concurrent users normal load, ~500 peak sessions.
- 7 microservices + gateway + 2 portals.
- Managed DB per service, encrypted at rest.
- Logs retained for operational troubleshooting (short), audit logs retained longer.

| Cost Dimension | All-in-One VM | Kubernetes (AKS/EKS/GKE) | App Service / PaaS |
|---|---|---|---|
| Compute baseline | **Low–Medium** (1–2 VMs) | **Medium–High** (cluster nodes + control plane) | **Medium** (app plans / consumption)
| Ops/Run cost (people) | **Low** | **High** (platform engineering) | **Medium–Low**
| Scaling cost | **Step-wise** (resize VM) | **Elastic** (HPA + nodes) but can be **higher** | **Elastic** (scale units) moderate
| Availability cost (HA) | **High** (multi-VM + LB) | **Medium** (multi-zone nodes) | **Medium** (multi-instance + zone)
| Networking/Ingress | **Low** | **Medium** (ingress, LB, NAT) | **Low–Medium**
| Observability ingestion | Similar across variants | Similar across variants | Similar across variants
| Typical total (relative) | **$** | **$$$** | **$$** |

**Rule of thumb**
- Choose **All-in-One VM** when you prioritize *lowest platform cost and simplest operations* and can tolerate limited elasticity.
- Choose **Kubernetes** when you prioritize *maximum scalability, resilience, and fine-grained controls*, and you have platform ops maturity/budget.
- Choose **App Service/PaaS** when you want *good scalability/resilience with lower ops overhead* than Kubernetes.

### 9.3 Recommendation Path (Simple)

| If you need... | Prefer |
|---|---|
| Fast PoC / training environment | All-in-One VM |
| Enterprise-grade scale, multi-team microservices, frequent releases | Kubernetes |
| Managed platform with strong baseline security and simpler ops | App Service / PaaS |



---

## 10. Variant Trade-offs (What You Gain vs What You Pay)

This section captures the **primary trade-offs** for each deployment variant, focusing on scalability, reliability, security posture, operational burden, and cost characteristics.

### 10.1 Variant A — All-in-One VM (Single Host)

**Best for**: PoC / training / small pilot, where simplicity and speed matter most.

**Trade-offs**
- ✅ **Simplicity**: Single host is easy to understand, deploy, and troubleshoot.
- ✅ **Lowest platform overhead**: No cluster control-plane or orchestrator to run.
- ✅ **Predictable baseline cost**: Fixed-size VM(s) makes spend predictable.
- ⚠️ **Limited elasticity**: Scaling is mostly vertical (bigger VM) or manual multi-VM.
- ⚠️ **Resilience risk**: Single host is a single point of failure unless HA is added (which increases cost/complexity).
- ⚠️ **Security blast radius**: A compromise/misconfiguration can impact many components because they co-reside.
- ⚠️ **Change risk**: Deployments can be more “big bang” unless you build strong release automation.

**Mitigations**
- Add a second VM and load balancer for HA; separate services into processes/containers; harden OS, patch regularly; restrict ports; enforce backups and disaster recovery runbooks.

---

### 10.2 Variant B — Kubernetes (AKS/EKS/GKE)

**Best for**: Enterprise scale, multi-team delivery, frequent releases, and strong resilience.

**Trade-offs**
- ✅ **Maximum scalability**: Horizontal scaling (HPA) and node pool scaling are first-class.
- ✅ **Resilience & self-healing**: Pods restart, re-schedule, and roll during upgrades.
- ✅ **Strong isolation controls**: Namespace boundaries, network policies, pod security standards, and (optionally) service mesh for mTLS.
- ✅ **Modern delivery**: Blue/green, canary, progressive delivery patterns are natural.
- ⚠️ **Highest operational complexity**: Requires platform engineering skills (cluster ops, upgrades, networking, ingress, policies).
- ⚠️ **Cost overhead**: Cluster baseline (nodes + supporting components) can be higher even at low load.
- ⚠️ **Security is powerful but non-trivial**: Misconfigured RBAC/network policies/ingress can create gaps.

**Mitigations**
- Use managed Kubernetes; standardize helm charts; implement policy-as-code (OPA/Gatekeeper), secrets management, and automated cluster upgrades; introduce service mesh only when needed.

---

### 10.3 Variant C — App Service / PaaS (Web Apps + Managed Services)

**Best for**: Balanced approach—good scalability and resilience with reduced ops burden.

**Trade-offs**
- ✅ **Reduced operational burden**: Managed runtime (patching, scaling primitives, basic health management).
- ✅ **Fast delivery**: Easier to provision and run than Kubernetes.
- ✅ **Good scale for many workloads**: Horizontal scaling via instances; supports staged deployments.
- ✅ **Security baseline**: Managed identity + private endpoints are commonly available patterns.
- ⚠️ **Less control than Kubernetes**: Network-level micro-segmentation and custom scheduling are limited.
- ⚠️ **Platform constraints**: Runtime limits, supported networking patterns, and extension models may constrain advanced scenarios.
- ⚠️ **Cost at sustained high load**: Can become expensive if you need many always-on instances.

**Mitigations**
- Use private endpoints to data; apply gateway/WAF policies; use multiple app plans for isolation; adopt async messaging for spikes; monitor scale-out thresholds carefully.

---

### 10.4 Summary of Trade-offs (One View)

| Dimension | All-in-One VM | Kubernetes | App Service / PaaS |
|---|---|---|---|
| Speed to start | Fastest | Medium | Fast |
| Operational burden | Low | High | Medium-Low |
| Scalability ceiling | Low-Medium | Highest | High |
| Resilience | Low unless HA added | Highest | High |
| Security isolation | Low | Highest | High |
| Typical cost curve | Low baseline, step-wise | Higher baseline, elastic | Medium baseline, elastic |

