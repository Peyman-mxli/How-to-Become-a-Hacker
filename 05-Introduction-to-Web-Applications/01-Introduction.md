# Lesson 01/17 — Introduction

## Learning objectives
Explain web applications and client-server architecture, distinguish static websites and native applications, understand software distribution models, and identify major web security risks.

## Web applications and architecture
A web application provides interactive functionality through a browser. A typical client-server application separates the user interface from server-side processing. HTML describes page structure, CSS presentation, and JavaScript behavior. Backend code processes requests, enforces business rules and permissions, and may access a database. A database is common but not mandatory. The browser receives client-side assets; it does not normally receive the backend source code.

Independent example: a school attendance application displays a dashboard in the browser, sends an HTTPS request to the backend, checks whether a teacher is permitted to view the requested class, retrieves the permitted records, and returns a representation. The frontend renders the result. This is a conceptual example rather than an audit of an existing project.

## Static websites and dynamic applications
A traditional static website serves prebuilt content, whereas a dynamic web application can adapt content to user input, session state and server data. Web 1.0 and Web 2.0 are broad historical descriptions, not strict protocol versions. A static site can still run JavaScript, and responsive design, modularity and cross-platform compatibility are design properties rather than guaranteed features of web applications.

| Aspect | Static website | Dynamic web application |
| --- | --- | --- |
| Content | Usually prebuilt | May depend on input and state |
| Example | Public information page | Attendance dashboard |
| Updates | Deploy revised content | May change as application data changes |
| Server logic | Often minimal | Frequently includes business logic |

## Web applications and native applications
Browser-based software can be delivered centrally across platforms; native applications are designed for particular operating systems and can integrate closely with hardware and system libraries. Browser applications still use local CPU, memory, storage and cache. Compatibility, performance, offline capability and update timing vary. Progressive web apps can use supported browser features such as offline caching and installation; hybrid apps can combine web technology with native containers.

## Distribution models
Open-source software is distributed under licenses that permit inspection and specified forms of use, modification and redistribution. Proprietary software restricts those rights. Hosted subscription services and source-code licensing are separate concepts.

The lesson names WordPress, OpenCart and Joomla as open-source examples, and Wix and Shopify as proprietary hosted offerings. Technical clarification: distinguish the open-source WordPress software (wordpress.org) from its hosted service (wordpress.com). The lesson also groups DotNetNuke with proprietary applications, but DNN Platform is open source and commercial offerings exist separately. Check the license of the actual edition.

## Security risks and testing
Internet-facing applications can expose many endpoints, roles, inputs, dependencies and data flows. Vulnerabilities may cause unauthorized disclosure, modification, service disruption or server compromise. Complexity and change can introduce new flaws; patch verification and regression testing are essential.

An authorized assessment begins by defining scope and testing rules, understanding architecture, examining client-delivered HTML/CSS/JavaScript and network traffic, then evaluating backend behavior and access controls. Compare unauthenticated and authenticated states where scope allows. Use dedicated test accounts and synthetic data, avoid disrupting services, and report findings responsibly. OWASP WSTG provides a structured testing reference.

| Risk | Potential impact | Defensive principle |
| --- | --- | --- |
| Sensitive data exposure | Unintended disclosure | Minimize exposed data, protect secrets, use TLS |
| Cross-site scripting (XSS) | Untrusted scripts execute in a user's browser | Context-aware output encoding and safe DOM APIs |
| SQL injection | Untrusted input changes database query semantics | Parameterized queries and least privilege |
| File inclusion | Unintended files are read or interpreted | Fixed allowlisted paths and safe file handling |
| Unrestricted file upload | Unsafe content storage or execution | Validate content, restrict storage and execution |
| IDOR | Unauthorized access to a referenced object | Check object-level permissions server-side |
| Broken access control | Unauthorized actions or privilege escalation | Deny by default and enforce role/function permissions |

The lesson also discusses how exposed identity information could increase risk to other services, including credential attacks. Treat this as an illustration of chained impact, not a procedure to perform against real systems. Remote code execution means a flaw permits code execution on a remote system; whether a given issue permits it depends on the deployment and controls.

## Additional original example: authorization design
Consider a fictional school API with a route GET /api/classes/42/attendance. An authenticated student, teacher and administrator should have different permissions. The server must check the current user's role and relationship to class 42, not merely whether a session exists. A hidden button is not an access control.

Illustrative test design on an owned local API using synthetic records:

| Test identity | Expected behavior |
| --- | --- |
| No session | Authentication required |
| Assigned teacher | Only authorized class records returned |
| Unassigned teacher | Access denied or existence concealed |
| Administrator | Access according to explicitly configured policy |

These are proposed expectations, not observed test results. Exact HTTP codes depend on the API contract.

Illustrative read-only command against an owned local application, assuming this route exists:
~~~bash
curl -i http://127.0.0.1:5000/api/classes/42/attendance
~~~
-i includes response headers, GET is the default, and the URL identifies a hypothetical resource. No request was executed. A response such as 401 or 403 alone does not prove all access controls are correct.

## Verification
Section 1 theory studied and documented. No mandatory questions appear in the supplied lesson. No optional practice or installation is needed. HTB section completion not separately confirmed.

## References
- [HTB Academy — Introduction to Web Applications](https://academy.hackthebox.com/app/module/75)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Top Ten](https://owasp.org/www-project-top-ten/)
- [OWASP SQL Injection Prevention](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html)
- [OWASP IDOR Prevention](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)
- [MDN Progressive Web Apps](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps)
- [WordPress open-source software](https://wordpress.org/)
- [DNN Platform source](https://github.com/dnnsoftware/Dnn.Platform)

Independent English study notes based on the supplied lesson, with original defensive examples and clearly identified technical clarifications.
