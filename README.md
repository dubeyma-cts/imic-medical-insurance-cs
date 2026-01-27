# Indian Medical Insurance Corporation (IMIC)

## 📌 Overview 
A .NET-based, cloud-ready medical insurance platform for policy management, claims, payments, and member services.
IMIC is built using modern .NET technologies and follows domain-driven design (DDD), microservices-based patterns, and clean architecture principles.
The platform is designed to be:

- **Scalable** (supports millions of policies/claims)
- **Secure** (OIDC, OAuth2, RBAC, API security standards)
- **Extensible** (new modules can be added easily)
- **Cloud-read** (Azure-native support)
- **Observable** (OpenTelemetry, structured logging)
- **Maintainable** (clear separation of concerns & modular services)


## 📂 Project Folder Structure - Navigation

- **imic-medical-insurance-cs** #Root repository
- **Code** → `imic-medical-insurance-cs/src/` #Application source code
- **Services** → `imic-medical-insurance-cssrc/Services/` #Microservices (Policy, Claims, Payments, Members)
- **Web** → `imic-medical-insurance-cs/src/Web/` #Web portal (ASP.NET MVC/Blazor)
- **Gateways** → `imic-medical-insurance-cs/src/Gateways/` #API Gateway
- **Shared** → `imic-medical-insurance-cssrc/Shared/` #Shared domain, application, infrastructure
- **Tests** → `imic-medical-insurance-cs/tests/` #Unit, Integration, E2E tests
- **Tools** → `imic-medical-insurance-cs/tools/` #Scripts, infrastructure helpers
- **Docs** → `imic-medical-insurance-cs/docs/` #Architecture, ADRs, diagrams, specs
- **Architecture** → `imic-medical-insurance-cs/docs/architecture/` #standards, templates, checklists, decision log
- **Data** → `imic-medical-insurance-cs/docs/data/`
- **Diagrams** → `imic-medical-insurance-cs/docs/diagrams/`
- **Quality & NFRs** → `imic-medical-insurance-cs/docs/quality/`
- **Security** → `imic-medical-insurance-cs/docs/security/`
- **Specs** → `imic-medical-insurance-cs/docs/Specs/`
- **Testing** → `imic-medical-insurance-cs/docs/testing/`
- **Use-Cases** → `imic-medical-insurance-cs/docs/use-cases/`
- **ADRs** → `imic-medical-insurance-cs/docs/adr/`
- **ReadMe** → `imic-medical-insurance-cs/README.md` #Project Information
- **Core Case Study** → `imic-medical-insurance-cs/case_study_2_imic.md` #Case Study Details
- **Contributors** → `imic-medical-insurance-cs/CONTRIBUTING.md` #Project Contributors/Team
- **Api Versons** → `imic-medical-insurance-cs/VERSION`

## ✨ Features

IMIC provides a comprehensive set of capabilities required for a modern, large‑scale medical insurance platform. Each feature aligns with regulatory, security, and performance standards expected in enterprise insurance systems.

### 🧾 Policy Management
- Create, renew, and cancel insurance policies
- Automated premium calculation & product rules engine
- Policy endorsements (address change, member addition, coverage updates)
- Policy schedule generation (PDF/Digital)
- Policy transactions audit trail

### 👥 Member Management
- Member onboarding & KYC processing
- Eligibility checks & linking with policies
- Demographic updates with approval workflow
- Member lifecycle dashboard

### 🏥 Claims Management
- Digital claims submission & document upload
- Rules-based claim validation & adjudication engine
- Multi‑stage approval workflows
- Reimbursement & cashless claims support
- Real-time claim status tracking

### 💳 Payments & Billing
- Premium billing (monthly/quarterly/yearly)
- Integration with payment gateways (UPI/Netbanking/Cards)
- Auto-debit (eNACH)
- Refund processing & payout disbursement
- Ledger and accounting sync

### 🌐 Provider Network
- Hospital provider registry
- Empanelment workflows
- Provider contract management
- Provider claims validation workflows

### 🔐 Identity & Security
- OAuth2 / OpenID Connect (Azure Entra ID)
- Role & claims-based access control (RBAC/CBAC)
- API security (JWT, scopes, rate limiting)
- Data encryption at rest and in transit
- secure logging

### 🧱 Enterprise Architecture
- Modular microservices architecture (Policy, Claims, Payments, Members)
- Clean Architecture + DDD + CQRS patterns
- Unified API Gateway with centralized routing
- Separate databases per service

### 📈 Observability & Reliability
- Structured logging (Serilog)
- Metrics & dashboards (Prometheus/Grafana/Azure Monitor)
- Resilience patterns (Polly: retries, circuit breakers)
- Health checks & liveness/readiness probes

### 🧪 Quality & Automation
- Unit, integration, contract, and E2E tests
- Automated CI/CD with GitHub Actions
- Code scanning (CodeQL), dependency health checks
- Static analysis (SonarQube optional)
- Infrastructure pipelines

### 🖥️ Web Portal
- Member self-service portal
- Admin & operations dashboards
- Claims status, policy download, premium payment
- Responsive design for desktop/mobile

### 🛠️ Extensibility
- Event-driven integration ready (Azure Service Bus/Kafka)
- Plugin architecture for new policy products
- Easy onboarding of third-party systems (TPA, providers, payment gateways)
- Versioned APIs with backward compatibility

## 🛠️ Tech Stack (Example)
- **Frontend**: Angular / HTML / CSS  
- **Backend**: Asp.Net
- **Database**: MySQL  
- **Tools**: Postman, Git, VS Code  

---

## 📸 Screenshots  
(Add images in `docs/screenshots/`)

---

## 🧪 Testing
- Test cases for backend & frontend are included under /tests.

---
## 🙌 Author  
- Manish Kumar Dubey 
