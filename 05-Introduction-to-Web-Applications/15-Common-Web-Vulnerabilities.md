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

## 11. Expanded lesson reference

### Terminology

| Term | Meaning |
| --- | --- |
| Misconfiguration | Insecure settings or deployment choices |
| Attack vector | Path by which a weakness can be reached |
| Privilege escalation | Gaining permissions beyond intended scope |
| Authentication bypass | Circumventing identity verification |
| RCE | Remote code execution, a possible impact rather than a specific root cause |
| PoC | Proof of concept demonstrating a claim |
| Mitigation | Measure reducing risk |
| Remediation | Correcting the underlying weakness |

OWASP (Open Worldwide Application Security Project) is a nonprofit foundation offering community security resources. The [Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/) provides testing methodology; the [Cheat Sheet Series](https://cheatsheetseries.owasp.org/) provides implementation advice.

### Historical examples in the supplied course

| Product and version named by HTB | Underlying issue | Potential consequence |
| --- | --- | --- |
| College Management System 1.2 | Unsafe SQL construction in login handling | Authentication bypass |
| Responsive Thumbnail Slider 1.0 | Inadequate file type and handler restrictions | Unauthorized file upload or execution |
| Plainview Activity Monitor 20161228 | Unsafe operating-system command construction | Unauthorized command execution |

These are historical course examples, not current vulnerability findings or proof that another installation is affected. Exploit payloads are not reproduced.

### Distinguishing security boundaries

```text
Untrusted input -> application -> browser rendering: XSS
                               -> SQL interpreter: SQL injection
                               -> OS command interpreter: command injection
                               -> upload processor: unsafe file handling
                               -> identity/permission checks: authentication or authorization failure
```

CSRF is different: a browser may send an unwanted request using an existing authenticated session. Use context-aware encoding for XSS and anti-CSRF tokens plus appropriate cookie policies for state-changing cookie-authenticated routes. See Sections 9 and 10 for more details.

### Project comparison

| Area | UniTrack | ProTrack-MX |
| --- | --- | --- |
| Authentication | Password hashing, session and MFA review | Supabase Auth and session handling |
| Authorization | Student, teacher, worker, visitor and administrator boundaries | RLS, group membership and role policies |
| Uploads | Visitor/profile photographs and credentials | Chat attachments, voice, video and room images |
| Command execution | Review external tools and subprocess usage | Review backend functions and tools |
| SQL | ORM/driver parameter binding | Safe SQL functions and scoped queries |
| Dependencies | Python package review | npm package review |

### CVE, CWE, CVSS and NVD

CVE identifiers follow CVE-year-sequence format; the year identifies the record's numbering year and does not necessarily establish discovery date. CWE classifies underlying weaknesses. CVSS provides standardized severity metrics. The NVD is operated by NIST. Vendor security advisories document product-specific affected versions and fixes.

| CWE | Weakness |
| --- | --- |
| CWE-78 | OS command injection |
| CWE-79 | Cross-site scripting |
| CWE-89 | SQL injection |
| CWE-287 | Improper authentication |
| CWE-352 | Cross-site request forgery |
| CWE-434 | Unrestricted upload of file with dangerous type |
| CWE-862 | Missing authorization |

The common CVSS v3.x/v4.0 qualitative scale is None (0.0), Low (0.1–3.9), Medium (4.0–6.9), High (7.0–8.9), Critical (9.0–10.0). Always record the CVSS version; severity is not identical to organizational risk.

### Shellshock — historical public research

NVD and Red Hat identify CVE-2014-6271 as a GNU Bash parsing flaw involving function definitions conveyed in environment variables. Vulnerable parsing could cause unintended commands to run in certain contexts. Both records associate the issue with CWE-78. Consult the exact vendor package advisory rather than assuming upstream version numbers alone establish exposure.

Sources checked November 26, 2026:
- [NVD: CVE-2014-6271](https://nvd.nist.gov/vuln/detail/CVE-2014-6271)
- [Red Hat: CVE-2014-6271](https://access.redhat.com/security/cve/cve-2014-6271)
- [Red Hat Shellshock advisory](https://access.redhat.com/security/vulnerabilities/shellshock)
- [CWE-78](https://cwe.mitre.org/data/definitions/78.html)
- [FIRST CVSS](https://www.first.org/cvss/)

The public classification is documented as general learning material; no separate assessment answer key, exploitation instructions or private lab data is included.
