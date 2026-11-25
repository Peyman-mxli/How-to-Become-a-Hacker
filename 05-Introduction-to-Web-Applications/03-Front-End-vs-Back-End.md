# Lesson 03/17 — Front End vs. Back End

## Learning objectives
Distinguish client-side and server-side development, identify backend components, understand white-box and black-box assessment approaches, and recognize common security design mistakes.

## Front end
HTML defines document structure, CSS controls presentation, and JavaScript implements browser-side behavior. Frontend work includes visual design, UI, UX, accessibility, responsiveness, compatibility and performance. Client-side processing can be a performance bottleneck even when the network and backend are healthy. Modern backends can also be cross-platform; the source lesson's platform distinction is not an absolute rule.

Original self-contained example for an owned local HTML file (illustrative, not executed):

~~~html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Attendance Demo</title>
  <style>
    button { padding: 0.75rem; background: navy; color: white; }
  </style>
</head>
<body>
  <h1>Attendance Demo</h1>
  <button id="check" type="button">Check status</button>
  <p id="status" aria-live="polite"></p>
  <script>
    document.querySelector('#check').addEventListener('click', () => {
      document.querySelector('#status').textContent = 'Demo button clicked';
    });
  </script>
</body>
</html>
~~~

The heading, button and paragraph establish structure; the style rule changes the button appearance; the event listener updates the paragraph when the button is clicked. textContent inserts text rather than interpreting it as HTML. This example is a UI demonstration only, not an authentication or attendance system.

## Back end

| Component | Role | Examples |
| --- | --- | --- |
| Host and operating system | Provide compute and execution environment | Linux, Windows, virtual machines, containers |
| Web server | Handle HTTP traffic and route requests | Apache, NGINX, IIS |
| Database | Persist and retrieve information | MySQL, PostgreSQL, Microsoft SQL Server, Oracle, MongoDB |
| Application framework | Implement routes, business logic and services | Laravel/PHP, ASP.NET/C#, Spring/Java, Django or Flask/Python, Express/Node.js |

NoSQL describes a family of nonrelational data systems, not one specific database product. Backend responsibilities include implementing core logic, APIs, data management, libraries and integrations. Not every web application requires a custom backend or database; browser-only applications can provide substantial functionality.

Containers can separate applications and databases into distinct execution environments, but they generally share a host kernel and do not automatically eliminate cross-component risks. Limit network access, privileges, mounted files and credentials. Dedicated hosts or VMs offer other isolation trade-offs.

## Frontend and backend security
Client input is untrusted even when the UI restricts it. SQL injection occurs when untrusted input changes database query semantics. OS command injection occurs when input is interpreted as an operating system command. Prefer parameterized SQL and avoid invoking shell commands with user input. Local and remote file inclusion involve unintended file access or interpretation. Use fixed or allowlisted paths and avoid exposing backend files and secrets.

White-box testing uses authorized access to source code and internal design; black-box testing works from externally observable behavior without internal knowledge. Gray-box testing uses limited internal knowledge or test accounts. Reviewing client-delivered code can be part of a black-box assessment; source availability alone does not define the entire assessment methodology. Open-source backend code can support review, but deployed configuration and versions may differ. All testing must remain within authorized scope.

## Twenty security mistakes from the lesson
The following table preserves the lesson's order with independent explanations. Some source labels are ambiguous; the interpretations are explicitly identified.

| No. | Risk or mistake | Defensive response |
| --- | --- | --- |
| 1 | Accepting invalid database input | Validate types, lengths and business rules on the server; enforce database constraints. |
| 2 | Reviewing only the overall system | Examine individual components, trust boundaries and interactions. |
| 3 | Inventing custom security mechanisms | Use reviewed libraries and standards. |
| 4 | Adding security only at the end | Include threat modeling, secure development and tests throughout the lifecycle. |
| 5 | Storing plaintext passwords | Use salted password hashing such as Argon2id or scrypt. |
| 6 | Weak passwords | Encourage long unique passwords, block known compromised values and support MFA. |
| 7 | Unprotected stored data | Classify sensitive data and apply suitable encryption and access controls; not all database fields require encryption. |
| 8 | Overreliance on client-side checks | Enforce validation and authorization on the backend. |
| 9 | Assuming everything will work safely | Consider abuse cases, failures and unexpected input. |
| 10 | Unsafe URL path variables | Validate resource identifiers and check object-level authorization; path parameters are not inherently insecure. |
| 11 | Blind trust in third-party code | Review dependencies, pin appropriate versions and monitor vulnerabilities. |
| 12 | Hard-coded backdoor accounts | Remove hidden accounts and manage identities and secrets securely. |
| 13 | Unchecked SQL injection risks | Use parameterized queries and verify input handling with authorized tests. |
| 14 | Unsafe remote file inclusion | Restrict file sources and prohibit untrusted executable includes. |
| 15 | Insecure data handling | Minimize collected information and control access, transport, retention and disposal. |
| 16 | Incorrect encryption | Use appropriate authenticated encryption and sound key management. |
| 17 | Weak cryptographic systems | Use established algorithms and maintained libraries; do not invent cryptography. |
| 18 | Ignoring layer 8 | Informal reference to human and organizational factors; not an official OSI layer. Address training and workflows. |
| 19 | Inadequate review of user actions | The source phrase is ambiguous; interpreted as insufficient auditing and review. Log relevant actions without logging secrets. |
| 20 | Misconfigured web application firewall | Validate WAF configuration and coverage; do not treat it as a replacement for secure code. |

## OWASP Top 10 — 2021 edition used by the supplied lesson

| Category | Meaning |
| --- | --- |
| A01 Broken Access Control | Users can perform unauthorized operations or access protected objects. |
| A02 Cryptographic Failures | Sensitive information is inadequately protected. |
| A03 Injection | Untrusted input alters commands, queries or interpreted content. |
| A04 Insecure Design | Security controls are missing or inadequate in the design. |
| A05 Security Misconfiguration | Unsafe defaults or incorrect deployment settings expose resources. |
| A06 Vulnerable and Outdated Components | Dependencies or platforms contain unresolved weaknesses. |
| A07 Identification and Authentication Failures | Identity, credential or session controls are insufficient. |
| A08 Software and Data Integrity Failures | Software updates, data or build processes lack integrity assurance. |
| A09 Security Logging and Monitoring Failures | Events are inadequately detected, recorded or investigated. |
| A10 Server-Side Request Forgery (SSRF) | The server makes unintended requests based on untrusted input. |

This is the historical 2021 list presented by the lesson, not a claim that it is the latest edition.

## Additional original defensive example: a local school application
For a hypothetical Flask/SQLite attendance system, bind the requested student identifier as a parameter rather than building SQL by string concatenation:

~~~python
cursor.execute(
    "SELECT status FROM attendance WHERE student_id = ?",
    (student_id,)
)
~~~

The question mark is the SQLite parameter placeholder; the tuple supplies the value separately. Parameter binding prevents input from changing the SQL statement structure, but it does not establish permission to view a student's data. Validate the identifier and authorize the current user for that student before running the query. This is an illustrative snippet, not a modification to UniTrack.

In a React frontend backed by a managed database API, do not rely on hidden buttons for security. Enforce server-side or database policies on every protected operation; never expose privileged credentials to the browser. These are conceptual project applications, not verified findings.

## Verification
Section 3 theory studied and documented. No mandatory questions in the supplied lesson. Optional editor exercise skipped. Examples are illustrative and no security testing or code execution was performed. HTB section completion not separately confirmed.

## References
- [HTB Academy — Introduction to Web Applications](https://academy.hackthebox.com/app/module/75)
- [OWASP Top 10: 2021](https://owasp.org/Top10/2021/)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [OWASP Password Storage](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html)
- [OWASP Authorization](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
- [MDN Web development](https://developer.mozilla.org/en-US/docs/Learn_web_development)

Independent study notes based on the supplied lesson, with original examples and explicitly identified technical clarifications.
