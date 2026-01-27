# Functional Specification – Buying a Policy
## Scope
Covers creation of customer, dependents, document upload, premium calculation, submission, and issuance.
## Actors & Roles
- Agent (create & submit)
- Underwriter (approve/reject)
- Policyholder (read-only portal access post-issuance)
## Business Rules
- Entire application rejected if any member is rejected.
- Premium increases with cover and age; per-member premium aggregated.
## UI
- Agent Home → New Policy Holder → Policy Form
## Data Validation
- Age proof required for all members; medical report types per age.
## Security
- Role-based access; account lockout after 3 failed attempts.
## Audit
- All submissions and decisions logged with timestamp & user.
