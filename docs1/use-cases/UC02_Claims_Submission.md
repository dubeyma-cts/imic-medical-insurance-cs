# UC02 – Claims Submission & Decision

Primary Actor: Agent (on behalf of Policyholder)
Preconditions: Policy Active; claim docs available.
Trigger: Policyholder submits claim.

Main Success Scenario:
1. Agent logs in, records claim for member, uploads bills & reports.
2. System sets status=Sent for approval.
3. Company staff validates documents.
4. If accepted, sets status=Accepted and enters cheque details.
5. Policyholder sees status in portal.

Extensions:
E1. Rejected → status=Rejected with reason; policyholder notified.
