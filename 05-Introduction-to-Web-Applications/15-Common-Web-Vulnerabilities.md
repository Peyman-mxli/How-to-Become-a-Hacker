# Section 15 — Common Web Vulnerabilities

**Provider:** Hack The Box Academy  
**Date:** November 26, 2026  
**Status:** Theory documented; mandatory conceptual CVE classification researched from authoritative records; submission acceptance pending.

## 1. Vulnerability research
A vulnerability is a security weakness; an exploit takes advantage of that weakness; a payload is input used to produce an effect; an attack surface comprises exposed interfaces. Public software can have known implementation defects and deployment-specific misconfigurations. Absence of a known CVE does not prove safety. OWASP Top 10 is an awareness resource, not an exhaustive testing checklist. This lesson covers four selected vulnerability groups, not four separate official Top 10 headings.

## 2. Broken authentication and access control
Authentication verifies identity; authorization verifies a specific action/resource permission. Authentication bypass can occur when login or session logic is defective. Access control breaks when a caller accesses an object or function outside permitted scope. Horizontal escalation crosses peer accounts; vertical escalation crosses privilege levels. The supplied historical College Management System example illustrates SQL injection causing an authentication bypass; root cause and impact are distinct. Use secure password hashing, MFA, session revocation, rate limits, server-side role/object checks and deny-by-default authorization.

## 3. Malicious file upload
File uploads are untrusted bytes and metadata. A vulnerable upload handler may permit executable content, overwrite files, expose private data or exhaust resources. The historical WordPress example in the lesson concerns inconsistent filename validation and server execution rules. A filename alone is not evidence of executable content. Allowlist file types, check actual content, enforce limits, use generated names, isolate storage outside web roots, disable execution, enforce retrieval permissions and patch parsers. A valid image can still be unsafe to expose if it contains private data.

## 4. OS command injection
When untrusted input is interpreted as an operating system instruction, it can change the intended operation. A shell interpreter and a database query interpreter are different boundaries. The lesson's historical plugin example involves unsafe command construction from a submitted network value. Avoid shell commands where library functions suffice; use fixed executables and structured arguments with validation if needed. Restrict process privileges, environment, time and resources. Shell avoidance does not automatically eliminate argument injection.

## 5. SQL injection
If input is embedded directly in query syntax, the database may interpret data as instructions. The supplied PHP search example illustrates this risk. Potential impacts include unauthorized reads, updates, authentication bypass and, in certain configurations, wider compromise. Use parameterized queries, approved identifiers, least privilege and authorization. Validation alone is not a substitute for parameterization. Stacked statements and operating system execution depend on engine, driver and privileges.

## 6. Comparison

| Group | Security boundary | Primary defensive strategy |
| --- | --- | --- |
| Authentication | Identity verification | Secure login and sessions |
| Access control | Object/function permission | Enforce authorization on every request |
| Upload | File processing and execution | Validate content and isolate storage |
| OS injection | Process invocation | Avoid shell and restrict structured arguments |
| SQL injection | Query interpretation | Parameterize values |

## 7. Original local verification strategy
With fictional users and data in an owned test environment, use a permission matrix to check each role and object. Verify actual response content and side effects rather than only status codes. Upload valid and invalid benign image fixtures and verify safe handling. Review code for unnecessary shell execution and raw query construction. Test ordinary apostrophes in legitimate names to check parameter binding. Record expected result, observed result, evidence and remediation separately. These examples are suggested defensive checks, not performed tests or HTB exercises.

## 8. Projects
UniTrack: check login/session protections, QR and visitor permissions, upload handling, parameterized queries, and role-specific administration. ProTrack-MX: check RLS for grades/messages/groups, teacher-only operations and storage policies. A UI restriction is not authorization. These are study recommendations, not confirmed defects or changes to either project.

## 9. CVE classification research
CVE means Common Vulnerabilities and Exposures; a CVE identifier names a publicly recorded vulnerability. CWE (Common Weakness Enumeration) identifies a weakness class. CVSS describes severity using standardized metrics and is not a measure of actual organizational risk. NVD enriches CVE records; vendor advisories provide product-specific fixes. For the required question, the learner should locate the specified historical GNU Bash vulnerability in CVE/NVD, read the description, compare the vendor weakness classification to the four lesson categories, and submit the matching category. The exact assessment response is intentionally not reproduced as a submission answer. No exploitation, target connection or Pwnbox is required. Acceptance is not yet confirmed.

## 10. Further reading
- [Authentication and access control](Web-Vulnerabilities/01-Authentication-and-Access-Control.md)
- [Malicious file upload](Web-Vulnerabilities/02-Malicious-File-Upload.md)
- [Command injection](Web-Vulnerabilities/03-Command-Injection.md)
- [SQL injection](Web-Vulnerabilities/04-SQL-Injection.md)

References: [OWASP Top 10](https://owasp.org/www-project-top-ten/), [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/), [CVE](https://www.cve.org/), [NVD](https://nvd.nist.gov/), [Red Hat Shellshock advisory](https://access.redhat.com/security/vulnerabilities/shellshock). Independently written notes; examples illustrative; no exploit reproduction or live testing.
