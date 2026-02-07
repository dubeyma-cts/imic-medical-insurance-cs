# IMIC – Use Cases (Functional Requirements)

## Mermaid Diagrams

### 1) System Use-Case Diagram (High Level)

```mermaid
flowchart LR
  %% Actors
  PH["Policy Holder"]
  AG["Insurance Agent"]
  CS["Company Staff"]
  AUTH["Authentication Service"]

  %% System boundary (visual grouping)
  subgraph IMIC["IMIC System"]
    UC01(["UC-01 Agent Login"])
    UC02(["UC-02 Company Staff Login"])
    UC03(["UC-03 Policy Holder Login"])
    UC04(["UC-04 Lock Account (3 Invalid Attempts)"])
    UC05(["UC-05 Create Policy Holder & Application"])
    UC06(["UC-06 Submit Policy Application"])
    UC07(["UC-07 Approve/Reject Policy"])
    UC08(["UC-08 View Policy Status"])
    UC09(["UC-09 View Personal Details"])
    UC10(["UC-10 View Policy Details"])
    UC11(["UC-11 Submit Claim via Agent"])
    UC12(["UC-12 Process Claim"])
    UC13(["UC-13 View/Track Claim Status"])
  end

  %% Associations
  AG --> UC01
  CS --> UC02
  PH --> UC03

  AUTH --> UC04

  AG --> UC05
  AG --> UC06
  CS --> UC07
  AG --> UC08

  PH --> UC09
  PH --> UC10

  PH --> UC11
  AG --> UC11
  CS --> UC12
  PH --> UC13

  %% Dependencies (include/extend style using dotted arrows)
  UC01 -.-> UC04
  UC02 -.-> UC04
  UC03 -.-> UC04
  UC06 -.-> UC07
  UC11 -.-> UC12
  UC12 -.-> UC13
```

---

### 2) UC-05 + UC-06 + UC-07 (Buying a Policy End-to-End)

```mermaid
sequenceDiagram
  autonumber
  actor AG as Insurance Agent
  actor CS as Company Staff
  participant SYS as IMIC System

  AG->>SYS: Login (UC-01)
  SYS-->>AG: Authenticated session

  AG->>SYS: Create policy holder + members (UC-05)
  AG->>SYS: Enter policy cover & details
  SYS-->>AG: Premium calculated (primary + dependents)
  AG->>SYS: Upload docs for each member
  SYS-->>AG: Application saved (Draft/Created)

  AG->>SYS: Submit application (UC-06)
  SYS-->>CS: Route to approval queue

  CS->>SYS: Open application (UC-07)
  CS->>SYS: Verify documents (each member)
  alt All members pass
    CS->>SYS: Approve policy
    SYS-->>AG: Status = Approved/Active
    SYS-->>SYS: Issue credentials to policy holder
  else Any member fails
    CS->>SYS: Reject application (record reason)
    SYS-->>AG: Status = Rejected (entire application)
  end
```

---

### 3) UC-11 + UC-12 + UC-13 (Claim Lifecycle)

```mermaid
sequenceDiagram
  autonumber
  actor PH as Policy Holder
  actor AG as Insurance Agent
  actor CS as Company Staff
  participant SYS as IMIC System

  note over PH,SYS
    Policy is Active.
    Claim can be raised immediately after issuance.
  end

  PH->>AG: Provide claim request + bills + diagnostic reports
  AG->>SYS: Login (UC-01)
  SYS-->>AG: Authenticated session

  AG->>SYS: Enter claim details (UC-11)
  AG->>SYS: Upload/attach documents
  AG->>SYS: Submit claim
  SYS-->>SYS: Status = Sent for Approval (default)
  SYS-->>CS: Route to claims queue

  CS->>SYS: Review claim + validate docs (UC-12)
  alt Accepted
    CS->>SYS: Enter cheque/payment details
    SYS-->>SYS: Status = Accepted
  else Rejected
    CS->>SYS: Record rejection reason
    SYS-->>SYS: Status = Rejected
  end

  PH->>SYS: Login (UC-03)
  SYS-->>PH: Authenticated session
  PH->>SYS: View Claims Details (UC-13)
  SYS-->>PH: Show all claims + current status
```

---

### 4) UC-01/02/03 + UC-04 (Login With Account Lock Rule)

```mermaid
flowchart TD
  A[User enters username/password] --> B{Credentials valid?}
  B -- Yes --> C[Login success
Reset invalid counter]
  B -- No --> D[Increment invalid counter]
  D --> E{Attempts >= 3?}
  E -- No --> F[Show error
Allow retry]
  E -- Yes --> G[Lock account]
  G --> H[Show 'Account locked' message]
  H --> I[Manual reactivation required]
```
