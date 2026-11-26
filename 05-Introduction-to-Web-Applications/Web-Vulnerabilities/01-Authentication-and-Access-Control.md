# Broken Authentication and Broken Access Control

Authentication establishes identity; authorization decides whether a specific identity may perform an action on a specific resource. Broken authentication includes defective login, reset, MFA or session mechanisms. Broken access control includes absent object, function or tenant authorization. Horizontal escalation crosses peer accounts; vertical escalation crosses privilege levels. Authentication bypass can be an outcome of a different root cause, such as SQL injection.

## Original defensive example (Flask)
Assume trusted session middleware, an existing User model and SQLAlchemy instance:
```python
from flask import session, abort

@app.get("/admin/reports")
def admin_reports():
    uid = session.get("user_id")
    if uid is None:
        abort(401)
    user = db.session.get(User, uid)
    if user is None or not user.active:
        abort(403)
    if user.role != "administrativo":
        abort(403)
    return {"message": "Authorized access"}
```
session.get obtains a session value; db.session.get loads current account state; abort terminates request with an error code. This example assumes authenticated session establishment, revocation, CSRF for state-changing routes and secure session configuration elsewhere. Browser routes may instead redirect to login. HTTP 401 responses in an HTTP authentication scheme require an applicable challenge. Object-specific access additionally requires ownership or assignment checks. A user ID is not proof of identity or ownership.

## Defensive verification
Use fictional accounts on owned applications. Confirm unauthenticated access fails, students cannot access other students' private records, teachers are restricted to assigned groups, disabled accounts lose access, and admin-only operations are enforced server-side. Compare content and side effects as well as status codes. Do not enumerate real accounts or test outside scope.

## Controls
Use adaptive salted password hashes, MFA, login throttling, generic error responses, session rotation/revocation, secure cookies, short-lived single-use reset tokens, deny-by-default authorization, least privilege and centralized policies. Log security events without secrets. Avoid trusting role fields from client requests. ProTrack-MX needs PostgreSQL RLS and server-side permissions; UniTrack needs session, role and object checks. Examples are illustrative, not audit findings.

References: [Authentication](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html), [Authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html), [Session management](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html).

## Expanded concepts and permission matrix

Authentication answers "Who are you?" Authorization answers "What are you permitted to do?" Successful login does not imply access to all resources. Weak authentication may arise from defective credential checking, password recovery, session expiry, MFA or rate limiting. Missing access control can arise from trusting client-side role values or omitting object-level checks. IDOR/BOLA describes object authorization failures, not every request with an identifier.

| Operation | Student | Teacher | Administrator |
| --- | --- | --- | --- |
| Own profile | Allow | Allow | Allow |
| Manage grades for assigned groups | Deny | Allow within scope | Allow within policy |
| Manage accounts | Deny | Deny | Allow within policy |
| Approve visitor passes | Deny | Deny | Allow within policy |
| System administration | Deny | Deny | Allow within policy |

This matrix is illustrative, not a description of verified production settings. Students may separately be permitted to read their own grades. RBAC (role-based access control) maps roles to permissions; roles alone do not enforce ownership or tenant membership.

### Flask code reference

| Expression | Purpose |
| --- | --- |
| @app.get | Register a GET endpoint |
| session.get | Read the authenticated session value |
| db.session.get | Fetch an account by primary key |
| user.active | Check current account state |
| user.role | Evaluate server-side role |
| abort | Stop processing with a status code |

Reloading account state avoids relying solely on stale session role values. For invalid authentication, use an appropriate login redirect or HTTP authentication challenge; for authenticated forbidden access, use 403 or a deliberately concealed 404 where appropriate.

### Verification evidence template

Record test account role, permitted object, expected behavior, actual response and whether any side effects occurred. Use invented data, never private student information. Verify read and write controls independently. Do not claim a vulnerability merely because a page is discoverable.
