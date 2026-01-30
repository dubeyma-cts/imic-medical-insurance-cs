# Security Use Cases

## SUC01: User Authentication with Account Lockout

### Use Case ID
SUC01

### Use Case Name
User Authentication with Account Lockout Protection

### Primary Actor
Policy Holder / Agent / Company Staff

### Stakeholders
- User attempting to log in
- System Administrator
- Security Team

### Preconditions
- User has valid credentials
- User account exists in the system
- User account is not currently locked

### Main Success Scenario
1. User navigates to the login page
2. System displays login form with username and password fields
3. User enters username and password
4. System validates credentials
5. System resets failed login attempt counter to zero
6. System creates a new session with 15-minute timeout
7. System logs successful authentication event
8. System redirects user to appropriate home page based on role

### Extensions

**4a. Invalid Credentials - First or Second Attempt**
1. System increments failed login attempt counter
2. System logs failed authentication attempt with IP address and timestamp
3. System displays error message: "Invalid username or password. Attempt X of 3."
4. Use case returns to step 2

**4b. Invalid Credentials - Third Attempt**
1. System increments failed login attempt counter to 3
2. System locks the user account
3. System logs account lockout event
4. System sends notification to system administrator
5. System displays error message: "Account has been locked due to multiple failed login attempts. Please contact administrator."
6. Use case ends

**4c. Account Already Locked**
1. System displays error message: "This account is locked. Please contact administrator."
2. System logs attempted login on locked account
3. Use case ends

### Postconditions
**Success**: User is authenticated and redirected to role-appropriate home page
**Failure**: User account may be locked and administrator is notified

### Special Requirements
- Password must not be displayed in plain text
- Failed login attempts must be counted per account, not per session
- Lockout event must be immediately effective across all servers

### Frequency of Use
High - Multiple times per day per user

---

## SUC02: Manual Account Unlock

### Use Case ID
SUC02

### Use Case Name
Administrator Unlocks User Account

### Primary Actor
System Administrator (Company Staff with admin privileges)

### Stakeholders
- Locked user
- System Administrator
- Security Team

### Preconditions
- Administrator is authenticated and authorized
- User account is in locked state
- Administrator has received unlock request from user

### Main Success Scenario
1. Administrator logs into Company section
2. Administrator navigates to Account Management screen
3. System displays list of locked accounts
4. Administrator selects the locked user account
5. System displays account details including lock timestamp and failed attempt history
6. Administrator verifies user identity through established verification process
7. Administrator clicks "Unlock Account" button
8. System prompts for confirmation
9. Administrator confirms unlock action
10. System unlocks the account
11. System resets failed login attempt counter to zero
12. System logs account unlock event with administrator ID
13. System sends notification to user that account has been unlocked
14. System displays success message

### Extensions

**7a. Administrator Decides Not to Unlock**
1. Administrator reviews failed attempts and determines suspicious activity
2. Administrator escalates to security team
3. Use case ends

**10a. System Unable to Unlock**
1. System displays error message
2. System logs error
3. Administrator retries or escalates to technical support
4. Use case ends

### Postconditions
**Success**: User account is unlocked and user can attempt login
**Failure**: Account remains locked

### Special Requirements
- Only administrators can unlock accounts
- All unlock actions must be audited
- User must be notified when account is unlocked

### Frequency of Use
Low to Medium - As needed when accounts are locked

---

## SUC03: Session Timeout and Re-authentication

### Use Case ID
SUC03

### Use Case Name
Automatic Session Timeout After Inactivity

### Primary Actor
System

### Stakeholders
- Logged-in user (Policy Holder / Agent / Company Staff)
- Security Team

### Preconditions
- User is logged in with active session
- User has been inactive for specified period

### Main Success Scenario
1. User is logged in and viewing a page
2. System tracks last activity timestamp
3. User does not perform any action for 15 minutes
4. System detects session timeout condition
5. System invalidates the session
6. System logs session timeout event
7. User attempts to perform an action
8. System detects invalid session
9. System redirects user to login page
10. System displays message: "Your session has expired due to inactivity. Please log in again."

### Extensions

**3a. User Performs Action Before Timeout**
1. System resets inactivity timer
2. System continues normal operation
3. Use case returns to step 2

**8a. User Attempts to Access Sensitive Operation**
1. System immediately redirects to login page
2. System displays security message
3. Use case ends

### Postconditions
**Success**: Session is terminated and user must re-authenticate

### Special Requirements
- Session timeout must be enforced server-side
- Timeout period is 15 minutes of inactivity
- Client-side warning may be displayed 1 minute before timeout

### Frequency of Use
High - Occurs whenever user is inactive

---

## SUC04: Role-Based Access Control Enforcement

### Use Case ID
SUC04

### Use Case Name
Enforce Role-Based Permissions

### Primary Actor
System

### Stakeholders
- Any authenticated user
- Security Team

### Preconditions
- User is authenticated
- User has assigned role (Policy Holder / Agent / Company Staff)

### Main Success Scenario
1. User attempts to access a resource or perform an action
2. System identifies user's role
3. System checks if role has permission for requested action
4. Permission is granted for the role
5. System logs access with user ID and action
6. System allows the requested action
7. System displays the requested resource

### Extensions

**4a. Permission Denied**
1. System denies access
2. System logs unauthorized access attempt with details
3. System displays error message: "You do not have permission to access this resource."
4. System redirects to user's home page
5. Use case ends

**4b. Attempt to Escalate Privileges**
1. System detects privilege escalation attempt
2. System logs security event with high severity
3. System alerts security team
4. System terminates user session
5. System may lock account pending investigation
6. Use case ends

### Postconditions
**Success**: User accesses permitted resource
**Failure**: Access is denied and event is logged

### Special Requirements
- Permission check must occur on every request
- Role permissions must be enforced at application and data layers
- All denied access attempts must be logged

### Frequency of Use
Very High - Every user action

---

## SUC05: First-Time Login and Password Change

### Use Case ID
SUC05

### Use Case Name
Policy Holder First-Time Login with Mandatory Password Change

### Primary Actor
Policy Holder

### Stakeholders
- Policy Holder
- Insurance Company
- Security Team

### Preconditions
- Policy application has been approved
- Policy holder has received initial credentials via secure letter
- Account has been created with temporary password
- Account is marked for mandatory password change

### Main Success Scenario
1. Policy holder navigates to login page
2. Policy holder enters username and temporary password
3. System validates credentials
4. System detects first-time login flag
5. System logs first-time login event
6. System redirects to password change page
7. System displays password policy requirements
8. Policy holder enters current password
9. Policy holder enters new password twice
10. System validates new password against policy
11. System ensures new password is different from temporary password
12. System hashes and stores new password
13. System clears first-time login flag
14. System logs password change event
15. System displays success message
16. System redirects to policy holder home page

### Extensions

**10a. New Password Does Not Meet Policy**
1. System displays specific validation errors
2. Use case returns to step 8

**10b. New Password Same as Temporary Password**
1. System displays error: "New password must be different from temporary password."
2. Use case returns to step 8

**10c. Policy Holder Cancels Password Change**
1. System logs cancellation attempt
2. System logs out user
3. System displays message: "Password change is required. Please log in again to complete."
4. Use case ends

### Postconditions
**Success**: Policy holder has changed password and can access system
**Failure**: Account remains with temporary password and first-time login flag

### Special Requirements
- Password change must be enforced before any other system access
- Temporary password must be single-use or time-limited
- Strong password policy must be enforced

### Frequency of Use
Once per policy holder account

---

## SUC06: Secure Document Upload with Malware Scanning

### Use Case ID
SUC06

### Use Case Name
Upload and Scan Supporting Documents

### Primary Actor
Agent

### Stakeholders
- Agent
- Policy Holder
- Company Staff
- Security Team

### Preconditions
- Agent is logged in
- Agent is entering policy or claim details
- Documents have been received from policy holder

### Main Success Scenario
1. Agent navigates to document upload section
2. System displays allowed file types and size limits
3. Agent selects document file to upload
4. System validates file extension
5. System validates file size
6. System generates unique filename to prevent path traversal
7. System temporarily stores file in quarantine area
8. System initiates malware scan
9. Malware scanner analyzes file
10. Scanner returns clean result
11. System moves file to secure storage
12. System encrypts file at rest
13. System logs document upload with metadata
14. System displays success message to agent
15. Agent continues with application submission

### Extensions

**4a. Invalid File Type**
1. System displays error: "File type not allowed. Please upload PDF, JPG, or PNG files only."
2. Use case returns to step 3

**5a. File Size Exceeds Limit**
1. System displays error: "File size exceeds maximum allowed size of 10MB."
2. Use case returns to step 3

**10a. Malware Detected**
1. System quarantines file
2. System logs security event with high severity
3. System alerts security team
4. System deletes quarantined file
5. System displays error: "Security threat detected in file. Upload rejected."
6. Use case returns to step 3

**10b. Scanner Unavailable**
1. System logs error
2. System rejects upload
3. System displays error: "Unable to verify file security. Please try again later."
4. Use case ends

### Postconditions
**Success**: Document is securely stored and associated with application
**Failure**: Document is rejected and agent must provide alternative

### Special Requirements
- All uploads must be scanned before storage
- Rejected files must be permanently deleted
- Document access must be audited
- Documents must be encrypted at rest

### Frequency of Use
High - Multiple times per policy/claim application

---

## SUC07: Audit Trail for Policy Approval

### Use Case ID
SUC07

### Use Case Name
Comprehensive Audit Logging for Policy Approval/Rejection

### Primary Actor
Company Staff

### Stakeholders
- Company Staff
- Policy Holder
- Agent
- Auditors
- Compliance Team

### Preconditions
- Company staff is logged in
- Policy application is in "Pending" status
- All required documents have been reviewed

### Main Success Scenario
1. Company staff navigates to policy approval screen
2. System displays policy application details
3. Company staff reviews all submitted documents
4. Company staff selects "Approve" option
5. System logs approval action with:
   - Staff member ID and name
   - Policy application ID
   - Timestamp
   - Decision (Approved)
   - IP address and user agent
6. System changes policy status to "Active"
7. System logs status change
8. System sends notification to agent
9. System triggers policy document generation
10. System displays confirmation message

### Extensions

**4a. Policy Rejected**
1. Company staff selects "Reject" option
2. Company staff enters rejection reason
3. System logs rejection action with:
   - Staff member ID and name
   - Policy application ID
   - Timestamp
   - Decision (Rejected)
   - Rejection reason
   - IP address and user agent
4. System changes policy status to "Rejected"
5. System logs status change
6. System sends notification to agent with reason
7. System displays confirmation message
8. Use case ends

**3a. Staff Views Sensitive Document**
1. System logs document access with:
   - Staff member ID
   - Document ID and type
   - Timestamp
   - View action
2. Company staff continues review
3. Use case returns to step 3

### Postconditions
**Success**: Policy decision is recorded with complete audit trail
**Failure**: None - all actions are logged

### Special Requirements
- All approval/rejection actions must be logged
- Audit logs must be tamper-proof
- Audit logs must be retained for 7 years
- Logs must include sufficient detail for compliance audits

### Frequency of Use
High - Multiple times per day

---

## SUC08: Secure Password Reset

### Use Case ID
SUC08

### Use Case Name
User-Initiated Password Reset with Verification

### Primary Actor
Policy Holder / Agent

### Stakeholders
- User requesting reset
- Security Team

### Preconditions
- User has forgotten password
- User account exists and is not locked
- User has access to registered email

### Main Success Scenario
1. User clicks "Forgot Password" on login page
2. System displays password reset request form
3. User enters username or email address
4. System validates account exists
5. System generates secure reset token (time-limited, single-use)
6. System logs password reset request
7. System sends reset link to registered email
8. System displays message: "Password reset instructions sent to registered email"
9. User receives email and clicks reset link
10. System validates reset token (not expired, not used)
11. System displays new password form
12. User enters new password twice
13. System validates password against policy
14. System ensures password is different from previous passwords
15. System hashes and stores new password
16. System invalidates reset token
17. System logs password change event
18. System sends confirmation email
19. System displays success message with login link

### Extensions

**4a. Account Not Found**
1. System displays generic message: "If account exists, reset instructions will be sent"
2. System does not reveal account existence
3. Use case ends

**4b. Account Locked**
1. System logs attempt
2. System displays generic message
3. Use case ends

**10a. Token Expired**
1. System displays error: "Reset link has expired. Please request a new one."
2. Use case returns to step 1

**10b. Token Already Used**
1. System displays error: "Reset link has already been used."
2. Use case returns to step 1

**13a. Password Does Not Meet Policy**
1. System displays validation errors
2. Use case returns to step 12

**13b. Password Matches Recent Password**
1. System displays error: "Cannot reuse previous passwords"
2. Use case returns to step 12

### Postconditions
**Success**: User password is changed and user can log in
**Failure**: Password remains unchanged

### Special Requirements
- Reset tokens must expire within 1 hour
- Tokens must be cryptographically secure
- Process must not reveal whether account exists
- All password reset attempts must be logged

### Frequency of Use
Medium - As needed when users forget passwords

---

## SUC09: Monitoring Suspicious Activity

### Use Case ID
SUC09

### Use Case Name
Real-Time Detection and Alerting for Suspicious Activity

### Primary Actor
Security Monitoring System

### Stakeholders
- Security Team
- System Administrator
- All users

### Preconditions
- Security monitoring system is active
- Alert rules are configured
- Alert notification channels are operational

### Main Success Scenario
1. Security monitoring system continuously analyzes security logs
2. System detects normal user activity
3. System updates baseline behavior models
4. System continues monitoring

### Extensions

**2a. Multiple Failed Login Attempts Detected**
1. System detects multiple failed attempts across multiple accounts from same IP
2. System generates security alert
3. System sends notification to security team
4. System may temporarily block IP address
5. Use case continues

**2b. Unusual Access Pattern Detected**
1. System detects policy holder accessing unusually large number of records
2. System generates security alert
3. System logs detailed activity
4. System sends notification to security team
5. Security team investigates
6. Use case continues

**2c. Privilege Escalation Attempt**
1. System detects unauthorized role change attempt
2. System immediately blocks action
3. System generates critical security alert
4. System terminates user session
5. System may lock account
6. System notifies security team immediately
7. Use case continues

**2d. After-Hours Access by Agent**
1. System detects agent login outside business hours
2. System generates informational alert
3. System applies additional scrutiny to actions
4. System logs alert for review
5. Use case continues

**2e. Mass Data Export Attempt**
1. System detects attempt to export large amount of data
2. System blocks export action
3. System generates critical alert
4. System terminates session
5. System notifies security team
6. Use case continues

### Postconditions
**Success**: Suspicious activity is detected and appropriate actions taken
**Ongoing**: System continues monitoring

### Special Requirements
- Monitoring must be real-time
- Alerts must be prioritized by severity
- False positive rate must be minimized
- Security team must be able to respond quickly

### Frequency of Use
Continuous - 24/7 monitoring

---

## SUC10: Data Access Logging and Review

### Use Case ID
SUC10

### Use Case Name
Audit Log Review for Compliance

### Primary Actor
Auditor / Compliance Officer

### Stakeholders
- Compliance Team
- Regulatory Authorities
- Management

### Preconditions
- Auditor has appropriate credentials and permissions
- Audit logs are available and retained
- Audit period is defined

### Main Success Scenario
1. Auditor logs into audit review interface
2. System authenticates auditor
3. System displays audit log search interface
4. Auditor specifies search criteria (date range, user, action type)
5. System retrieves matching audit records
6. System displays audit records with:
   - Timestamp
   - User ID and role
   - Action performed
   - Resource accessed
   - Result (success/failure)
   - IP address
7. Auditor reviews records for compliance
8. Auditor exports filtered records for reporting
9. System logs auditor's access to audit logs
10. System generates export file
11. System displays download link

### Extensions

**5a. Too Many Records**
1. System displays count and suggests refining criteria
2. Auditor refines search
3. Use case returns to step 5

**7a. Suspicious Pattern Identified**
1. Auditor flags records for investigation
2. System adds investigator notes to audit trail
3. Auditor may initiate investigation workflow
4. Use case continues

**8a. Regular Compliance Report**
1. Auditor selects predefined report template
2. System generates compliance report
3. System includes required metrics and violations
4. Use case continues at step 10

### Postconditions
**Success**: Auditor has reviewed and exported required records

### Special Requirements
- Audit logs must be read-only for auditors
- Access to audit logs must itself be audited
- Audit logs must be searchable and filterable
- Export function must maintain log integrity

### Frequency of Use
Regular - Monthly/quarterly compliance reviews

---

## Summary

These security use cases address the key security requirements identified in the IMIC case study:

1. **SUC01** - Account lockout mechanism (explicit requirement)
2. **SUC02** - Manual unlock process (explicit requirement)
3. **SUC03** - Session timeout enforcement
4. **SUC04** - Role-based access control for three user types
5. **SUC05** - Secure initial credential handling
6. **SUC06** - Secure document handling with malware protection
7. **SUC07** - Comprehensive audit trail for critical operations
8. **SUC08** - Secure password reset workflow
9. **SUC09** - Proactive security monitoring
10. **SUC10** - Audit log review for compliance

Each use case supports the overall security posture required for a medical insurance application handling sensitive personal and health information.
