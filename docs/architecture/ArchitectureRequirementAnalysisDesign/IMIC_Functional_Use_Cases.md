# IMIC – Use Cases (Functional Requirements)

> Source: *IMIC Requirement Summary* (uploaded DOCX). citeturn1search1

## 1. Scope
This document defines **use cases** derived from the functional requirements for the Indian Medical Insurance Corporation (IMIC) system. The system has three primary user groups:
- **Policy Holder** (web/internet portal) citeturn1search1
- **Insurance Agent** (web/internet portal) citeturn1search1
- **Insurance Company Staff** (intranet/back-office portal) citeturn1search1

---

## 2. Actors
- **Policy Holder (PH)** – views policy, personal and claim details; tracks claim status. citeturn1search1
- **Insurance Agent (AG)** – onboards policy holders, submits policies and claims, views policy status, communicates with policy holders. citeturn1search1
- **Company Staff (CS)** – validates documents, approves/rejects policy applications and claims, enters payment/cheque details. citeturn1search1
- **Authentication Service (AUTH)** – locks accounts after invalid attempts (system actor). citeturn1search1

---

## 3. Use Case Catalog
| ID | Use Case | Primary Actor |
|---|---|---|
| UC-01 | Agent Login | AG |
| UC-02 | Company Staff Login | CS |
| UC-03 | Policy Holder Login | PH |
| UC-04 | Lock Account After 3 Invalid Login Attempts | AUTH |
| UC-05 | Create New Policy Holder & Policy Application | AG |
| UC-06 | Submit Policy Application to Company | AG |
| UC-07 | Approve / Reject Policy Application | CS |
| UC-08 | View Policy Status (Agent) | AG |
| UC-09 | View Personal Details (Policy Holder) | PH |
| UC-10 | View Policy Details (Policy Holder) | PH |
| UC-11 | Submit Claim Request via Agent | PH / AG |
| UC-12 | Process Claim (Approve / Reject) | CS |
| UC-13 | View Claims & Track Claim Status (Policy Holder) | PH |

> Note: “Policy Holder Communication” is listed as an agent menu option but not described with specific behaviors; it is therefore not expanded into a detailed use case beyond acknowledging the feature entry point. citeturn1search1

---

# 4. Detailed Use Cases

## UC-01 – Agent Login
**Primary Actor:** Insurance Agent (AG) citeturn1search1

**Goal:** Agent authenticates to access Agent Home Page features.

**Preconditions:**
- Agent account exists and is not locked. citeturn1search1

**Trigger:** Agent opens Agent Section home page and enters credentials. citeturn1search1

**Main Success Scenario (MSS):**
1. Agent navigates to Agent Section login area. citeturn1search1
2. Agent enters username and password.
3. System validates credentials.
4. System displays Agent Home Page menu options (New Policy Holder, Policy Status, Claims Management, Policy Holder Communication). citeturn1search1

**Extensions / Alternate Flows:**
- **E1: Invalid credentials**
  1. System increments invalid-attempt counter.
  2. System shows error message.
  3. If this is the 3rd consecutive failure, invoke **UC-04 Lock Account**. citeturn1search1

**Postconditions:**
- On success: authenticated session established.
- On failure: session not established; invalid-attempt counter updated.

---

## UC-02 – Company Staff Login
**Primary Actor:** Company Staff (CS) citeturn1search1

**Goal:** Staff authenticates to access Company Home Page.

**Preconditions:**
- Staff account exists and is not locked. citeturn1search1

**Trigger:** Staff opens Company Section home page and enters credentials. citeturn1search1

**MSS:**
1. Staff navigates to Company Section login area. citeturn1search1
2. Staff enters username and password.
3. System validates credentials.
4. System displays Company Home Page menu (Approve/Disapprove Policy, Claims Management). citeturn1search1

**Extensions:**
- Same as UC-01 E1 (invalid credentials → possible lock). citeturn1search1

**Postconditions:** Authenticated staff session established.

---

## UC-03 – Policy Holder Login
**Primary Actor:** Policy Holder (PH) citeturn1search1

**Goal:** Policy holder logs in to view personal, policy and claim details.

**Preconditions:**
- Policy is issued and **Active**.
- PH has received login credentials (username & password). citeturn1search1

**Trigger:** PH accesses Policy Holder portal login area and enters credentials. citeturn1search1

**MSS:**
1. PH opens Policy Holder Home Page login area. citeturn1search1
2. PH enters username and password.
3. System validates credentials.
4. System displays Policy Holder Home Page menu (Personal Details, Policy Details, Claims Details). citeturn1search1

**Extensions:**
- Same as UC-01 E1 (invalid credentials → possible lock). citeturn1search1

**Postconditions:** Authenticated PH session established.

---

## UC-04 – Lock Account After 3 Invalid Login Attempts
**Primary Actor:** Authentication Service (AUTH) citeturn1search1

**Goal:** Protect system by locking an account after three consecutive invalid login attempts.

**Preconditions:**
- User account exists.
- User has entered invalid credentials consecutively. citeturn1search1

**Trigger:** 3rd consecutive invalid login attempt for the same user. citeturn1search1

**MSS:**
1. System detects the 3rd consecutive invalid attempt.
2. System sets account status to **Locked**.
3. System denies login and informs user the account is locked.
4. System records audit entry (who, when, channel).

**Business Rules:**
- Locked accounts can be reactivated **only manually**. citeturn1search1

**Postconditions:**
- Account is locked and cannot authenticate until manually reactivated.

---

## UC-05 – Create New Policy Holder & Policy Application
**Primary Actor:** Insurance Agent (AG) citeturn1search1

**Goal:** Capture customer (policy holder) details, policy details, and member/dependent information to create a new policy application.

**Preconditions:**
- Agent is logged in (UC-01).
- Customer intends to buy a policy through an agent. citeturn1search1

**Trigger:** Agent selects **New Policy Holder** from Agent Home Page. citeturn1search1

**MSS:**
1. Agent opens New Policy Holder screen. citeturn1search1
2. Agent enters primary member (policy holder) personal details.
3. Agent adds dependent member(s) as applicable. citeturn1search1
4. Agent enters policy details, including policy cover amount (must meet minimum cover). citeturn1search1
5. System calculates premium based on cover amount, age of members, and other factors; includes premium for primary and all dependents. citeturn1search1
6. Agent attaches/records required documents for each member (medical reports, age proof, other supporting documents). citeturn1search1
7. System saves the policy application in **Draft/Created** state.

**Extensions:**
- **E1: Missing required documents for any member** → system flags validation errors; application cannot be submitted. citeturn1search1
- **E2: Cover amount below minimum** → system rejects entry and prompts correction. citeturn1search1

**Postconditions:**
- New policy holder record exists.
- Policy application is created and ready for submission.

---

## UC-06 – Submit Policy Application to Company
**Primary Actor:** Insurance Agent (AG) citeturn1search1

**Goal:** Submit completed policy application and documents to the insurance company for verification.

**Preconditions:**
- Agent is logged in (UC-01).
- Policy application exists with member details and documents. citeturn1search1

**Trigger:** Agent selects **Submit** for a created policy application.

**MSS:**
1. Agent reviews application details.
2. Agent submits application.
3. System marks application as **Submitted for Approval** (or equivalent) and routes it to Company Section for verification. citeturn1search1
4. System makes the application visible to Company Staff in Approve/Disapprove Policy queue. citeturn1search1

**Postconditions:**
- Application is available for Company review.

---

## UC-07 – Approve / Reject Policy Application
**Primary Actor:** Company Staff (CS) citeturn1search1

**Goal:** Verify documents and decide to approve or reject a submitted policy application.

**Preconditions:**
- Staff is logged in (UC-02).
- At least one policy application is in the review queue. citeturn1search1

**Trigger:** Staff selects a policy application from **Approve / Disapprove Policy** list. citeturn1search1

**MSS (Approve):**
1. Staff opens the application and reviews details for the primary member and dependents.
2. Staff verifies submitted documents for each member. citeturn1search1
3. Staff records verification outcome.
4. If all members pass, staff approves the policy application. citeturn1search1
5. System issues policy, sets policy status to **Active**, and triggers dispatch to policy holder. citeturn1search1
6. System creates policy holder login credentials and makes them available to the policy holder. citeturn1search1

**Extensions (Reject):**
- **E1: Any member rejected**
  1. Staff marks one or more members as rejected.
  2. System rejects the **entire policy application** (business rule). citeturn1search1
  3. Staff records rejection reason(s) (e.g., age constraints, invalid medical reports, payment failure/cheque bounce). citeturn1search1
  4. System updates application status to **Rejected** and makes status visible to Agent via Policy Status screen. citeturn1search1

**Postconditions:**
- If approved: policy active; credentials created; PH can log in and view details read-only. citeturn1search1
- If rejected: application rejected; reason recorded.

---

## UC-08 – View Policy Status (Agent)
**Primary Actor:** Insurance Agent (AG) citeturn1search1

**Goal:** Agent views status of all policies submitted.

**Preconditions:**
- Agent logged in (UC-01). citeturn1search1

**Trigger:** Agent selects **Policy Status** from Agent Home Page. citeturn1search1

**MSS:**
1. System displays a list of policy applications submitted by the agent.
2. Agent selects a policy application.
3. System shows current status (e.g., Submitted, Approved/Active, Rejected) and, if rejected, the rejection reason. citeturn1search1

**Postconditions:** None (read-only for agent).

---

## UC-09 – View Personal Details (Policy Holder)
**Primary Actor:** Policy Holder (PH) citeturn1search1

**Goal:** Policy holder views personal details entered by the agent.

**Preconditions:**
- PH logged in (UC-03). citeturn1search1

**Trigger:** PH selects **Personal Details**.

**MSS:**
1. System displays policy holder personal information.
2. System enforces **view-only** access. citeturn1search1

**Postconditions:** None.

---

## UC-10 – View Policy Details (Policy Holder)
**Primary Actor:** Policy Holder (PH) citeturn1search1

**Goal:** Policy holder views policy information and status.

**Preconditions:**
- PH logged in (UC-03). citeturn1search1

**Trigger:** PH selects **Policy Details**.

**MSS:**
1. System displays policy information (cover, members, premium summary) and policy status.
2. System enforces **view-only** access. citeturn1search1

**Postconditions:** None.

---

## UC-11 – Submit Claim Request via Agent
**Primary Actors:** Policy Holder (PH), Insurance Agent (AG) citeturn1search1

**Goal:** Submit a claim for a primary member or dependent immediately after policy issuance.

**Preconditions:**
- Policy is issued (Active) and PH is eligible to submit claim immediately after issuance. citeturn1search1
- Claim documents (hospital bills, diagnostic reports) are available. citeturn1search1
- Agent logged in (UC-01).

**Trigger:** PH requests agent to submit a claim with documents.

**MSS:**
1. PH provides claim request and required documents to agent. citeturn1search1
2. Agent opens **Claims Management** in Agent Section. citeturn1search1
3. Agent selects the relevant policy and member (primary or dependent). citeturn1search1
4. Agent enters claim details and uploads/records documents.
5. Agent submits the claim.
6. System sets claim status to **Sent for Approval** (default). citeturn1search1
7. System routes claim to Company Staff claims queue.

**Extensions:**
- **E1: Missing documents** → system prevents submission and lists required documents. citeturn1search1

**Postconditions:**
- Claim is created and visible to Company Staff for processing; PH can track status. citeturn1search1

---

## UC-12 – Process Claim (Approve / Reject)
**Primary Actor:** Company Staff (CS) citeturn1search1

**Goal:** Validate claim documents and accept or reject a claim.

**Preconditions:**
- Staff logged in (UC-02).
- Claim exists with status **Sent for Approval**. citeturn1search1

**Trigger:** Staff selects a claim from **Claims Management** queue. citeturn1search1

**MSS (Accept):**
1. Staff reviews claim details and validates documents. citeturn1search1
2. Staff accepts the claim.
3. Staff enters cheque/payment details.
4. System updates claim status to **Accepted** and records payment information. citeturn1search1
5. System makes updated status visible to policy holder in Claims Details. citeturn1search1

**Extensions (Reject):**
- **E1: Invalid or insufficient documentation**
  1. Staff rejects claim.
  2. Staff records rejection reason.
  3. System updates claim status to **Rejected** and exposes the status to policy holder. citeturn1search1

**Postconditions:**
- Claim is in final state Accepted (with cheque details) or Rejected (with reason). citeturn1search1

---

## UC-13 – View Claims & Track Claim Status (Policy Holder)
**Primary Actor:** Policy Holder (PH) citeturn1search1

**Goal:** Policy holder views all submitted claims and their current status.

**Preconditions:**
- PH logged in (UC-03). citeturn1search1

**Trigger:** PH selects **Claims Details**.

**MSS:**
1. System displays list of claims submitted for the policy holder and dependents. citeturn1search1
2. For each claim, system shows status (Sent for Approval / Accepted / Rejected).
3. System enforces **view-only** access. citeturn1search1

**Postconditions:** None.

---

## 5. Global Business Rules & Notes
- If **any one member** (primary or dependent) is rejected during policy approval, the **entire policy application is rejected**. citeturn1search1
- Premium is influenced by policy cover amount and member ages; premium is charged for primary member and all dependents. citeturn1search1
- Claim can be raised immediately after policy issuance and is routed through an agent. citeturn1search1
- Default claim status on submission is **Sent for Approval**; final outcomes are Accepted (with cheque details) or Rejected (with reason). citeturn1search1
- After **three consecutive invalid login attempts**, the account is locked and can be reactivated **only manually**. citeturn1search1
