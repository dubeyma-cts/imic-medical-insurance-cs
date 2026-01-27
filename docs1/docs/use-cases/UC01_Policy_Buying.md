# UC01 – Buying a Policy

Primary Actor: Agent (on behalf of Customer)
Preconditions: Agent authenticated; product & cover available; KYC docs ready.
Trigger: Customer requests to buy a policy with dependents.

Main Success Scenario:
1. Agent logs in and creates Customer (primary member).
2. Agent adds dependents and uploads age proof & medical reports per member.
3. System computes premium per member based on age & cover; totals for policy.
4. Agent submits application with documents.
5. Company underwriter reviews documents and approves policy for all members.
6. System issues policy number, sets status=Active, generates credentials for policyholder, and dispatches policy schedule.

Extensions:
E1. Any member fails validation → Entire application rejected; reasons captured.
E2. Payment check bounces → Application rejected.

Postconditions: Policy issued (Active) or Application rejected; audit log updated.
