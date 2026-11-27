# 05 — Introduction to Web Applications

**Provider:** Hack The Box Academy | **Tier:** 0
**Status:** Completed — HTB 17/17; 15/17 full lessons documented
**Started:** November 24, 2026 (learner local date)

## Lessons

1. [Introduction](01-Introduction.md) — theory documented; no mandatory questions in supplied lesson.
2. [Web Application Layout](02-Web-Application-Layout.md) — theory documented; no mandatory questions in supplied lesson.
3. [Front End vs. Back End](03-Front-End-vs-Back-End.md) — theory documented; no mandatory questions.
4. Section 4 — awaiting lesson.
5. Section 5 — CSS text alignment question accepted (learner confirmed); full lesson pending.
6. [JavaScript](06-JavaScript.md) — theory documented; question area not supplied.
7. [Sensitive Data Exposure](07-Sensitive-Data-Exposure.md) — theory documented; exposed-password retrieval verified from terminal output; answer acceptance pending.
8. [HTML Injection](08-HTML-Injection.md) — theory and mandatory question reasoning documented; acceptance pending.
9. [Cross-Site Scripting (XSS)](09-Cross-Site-Scripting-XSS.md) — theory documented; browser alert and cookie retrieval verified; answer acceptance pending.
10. [Cross-Site Request Forgery (CSRF)](10-Cross-Site-Request-Forgery-CSRF.md) — theory documented; no mandatory question in supplied lesson.
11. [Back End Servers](11-Back-End-Servers.md) — theory and required conceptual question reasoning documented; acceptance pending.
12. [Web Servers](12-Web-Servers.md) — theory and required conceptual question reasoning documented; acceptance pending.
13. [Databases](13-Databases.md) — theory and required conceptual question reasoning documented; acceptance pending.
14. [Development Frameworks & APIs](14-Development-Frameworks-and-APIs.md) — theory documented; mandatory GET lab verified from learner terminal output; submission acceptance pending.
15. [Common Web Vulnerabilities](15-Common-Web-Vulnerabilities.md) — theory and required CVE classification researched; submission acceptance pending.
16. [Public Vulnerabilities](16-Public-Vulnerabilities.md) — full theory and required CVSS v2 research documented; acceptance pending.
17. [Next Steps](17-Next-Steps.md) — final lesson, development roadmap and original reference app documented; no mandatory question supplied.

## Supplemental notes

- [HTML image elements](HTML-Image-Elements.md) — image syntax, accessibility and original examples; standalone question concept covered, full lesson pending.

- [CSS text alignment](05-CSS-Text-Alignment-Supplement.md) — Section 5 question concept, alignment values and multilingual examples.

## Individual framework and library guides

Read the four supplemental guides in order. Each explains architecture, core concepts, a comparable original attendance-counter example, expected behavior, use cases, security and official references.

1. [Angular](JavaScript-Frameworks/01-Angular.md)
2. [React](JavaScript-Frameworks/02-React.md)
3. [Vue.js](JavaScript-Frameworks/03-Vue.md)
4. [jQuery](JavaScript-Frameworks/04-jQuery.md)

| Technology | Main approach | Typical fit |
| --- | --- | --- |
| Angular | Components, templates, signals and services | Structured applications |
| React | JSX components and state | Interactive interfaces |
| Vue | Reactive state and declarative templates | Progressive adoption and applications |
| jQuery | Direct DOM manipulation and event handlers | Existing pages and targeted enhancements |

All four are free and open-source. Examples are illustrative; no installations, execution or additional HTB completion are claimed.

## Public vulnerability research guides

- [CVSS scoring](Public-Vulnerabilities/01-CVSS.md)
- [Vulnerability research and remediation](Public-Vulnerabilities/02-Vulnerability-Research.md)
- [Public vulnerability guide index](Public-Vulnerabilities/README.md)

## Web vulnerability guides

- [Authentication and access control](Web-Vulnerabilities/01-Authentication-and-Access-Control.md)
- [Malicious file upload](Web-Vulnerabilities/02-Malicious-File-Upload.md)
- [OS command injection](Web-Vulnerabilities/03-Command-Injection.md)
- [SQL injection](Web-Vulnerabilities/04-SQL-Injection.md)
- [Vulnerability guide index](Web-Vulnerabilities/README.md)

## Development framework guides

- [Laravel](Development-Frameworks/01-Laravel.md)
- [Express.js](Development-Frameworks/02-Express.md)
- [Django](Development-Frameworks/03-Django.md)
- [Ruby on Rails](Development-Frameworks/04-Ruby-on-Rails.md)
- [Flask](Development-Frameworks/05-Flask.md)
- [Framework guide index](Development-Frameworks/README.md)

## Database study guides

- [Relational databases](Databases/01-Relational-Databases.md)
- [NoSQL databases](Databases/02-NoSQL-Databases.md)
- [Firebase](Databases/03-Firebase.md)
- [Database guide index](Databases/README.md)

## Individual web server guides

- [Apache HTTP Server](Web-Servers/01-Apache-HTTP-Server.md)
- [NGINX](Web-Servers/02-NGINX.md)
- [Microsoft IIS](Web-Servers/03-Microsoft-IIS.md)
- [Apache Tomcat](Web-Servers/04-Apache-Tomcat.md)
- [Node.js](Web-Servers/05-NodeJS.md)
- [Web server comparison and reading index](Web-Servers/README.md)

Section 11 supplemental guides cover architecture, configuration, command explanations, original local examples, troubleshooting, security and project applications. No additional HTB lesson credit or lab execution is claimed.

## Learning objectives

Understand web application architecture, browser and backend responsibilities, common web technologies, security risks and authorized assessment methodology.

## Current checkpoint

Sections 1–3 studied and documented; learner advanced to Section 3. All three supplied lessons have no mandatory questions. Section 3 covers frontend/backend development, assessment approaches, security mistakes and OWASP Top 10 (2021). No lab execution or separate platform completion confirmation. A standalone HTML image-element question was subsequently covered; acceptance and its section number were not supplied. Learner subsequently reached Section 5 and supplied a CSS text alignment question; the concept is documented. Section 5 answer acceptance is now confirmed by the learner after correcting declaration formatting. Full Sections 4–5 and Section 4 answer acceptance remain pending. Learner advanced to Section 6; JavaScript theory documented, including DOM, AJAX and frameworks. Question area not supplied. Learner advanced to Section 7; sensitive data exposure theory documented. Learner retrieved the exposed credential from an HTML comment using cURL against the assigned Section 7 target. Learner advanced to Section 8; Section 7 acceptance was not separately shown. HTML Injection theory and mandatory answer reasoning documented from supplied code; no live test performed. Learner advanced to Section 9; Section 8 acceptance was not separately shown. XSS theory documented; learner screenshots verify the local browser alert and cookie retrieval on the assigned lab. Learner advanced to Section 10; Section 9 answer acceptance was not separately shown. CSRF theory and defensive examples documented; no mandatory question appears in supplied content. Section 11 back-end servers, software stacks, virtualization, hardware and security documented; mandatory conceptual question reasoning identified from the supplied stack table. No lab required; submission acceptance pending. Learner advanced to Section 12; Section 11 answer acceptance was not separately shown. Section 12 web server workflow, HTTP status codes, input formats, cURL and server technologies documented; required conceptual question verified against RFC 9110, submission acceptance pending. No lab required. Apache Tomcat and Node.js supplemental guides added. Learner advanced to Section 13; Section 12 answer acceptance was not separately shown. Section 13 relational and non-relational databases, SQL relationships, secure PHP queries and Firebase documented. Required conceptual question verified from Google's official database comparison; acceptance pending. Three supplemental database guides added; no live lab or paid resource required. Learner advanced to Section 14; Section 13 answer acceptance was not separately shown. Section 14 frameworks, APIs, HTTP parameters, SOAP and REST theory documented; five framework supplements added. Mandatory GET lab verified from learner terminal output: the requested identifier was supplied as a query parameter, and HTTP 200 with a plain response-body name was observed. The exact assessment answer and target address are intentionally omitted. Submission acceptance has not been separately confirmed. Learner advanced to Section 15; Section 14 submission acceptance was not separately shown. Section 15 common web vulnerabilities documented, including authentication/access control, file upload, OS command injection and SQL injection; four defensive supplements added. Required CVE classification researched from NVD, CVE and vendor sources; no lab needed; submission acceptance pending. Learner advanced to Section 16; Section 15 submission acceptance was not separately shown. Section 16 public vulnerability research, CVE/CWE/CPE, CVSS v2/v3/v4, backend components and remediation documented, with two detailed supplements. Required CVSS v2.0 score corroborated by NVD-derived Tenable and Aqua records; NVD detail page was unreadable in this session. No lab required; acceptance pending. Learner advanced to Section 17; Section 16 submission acceptance was not separately shown. Final Next Steps lesson, nine-step development roadmap, original local reference application, defensive verification and recommended next modules documented. No mandatory question appears in supplied Section 17. Eight additional Section 16 resource tutorials are documented. HTB completion confirmed by the learner's share preview on November 26, 2026 (local date). All 17 platform sections are complete; 15 full lesson notes and two partial supplemental sections are documented. Full Sections 4–5 remain documentation gaps. Recommended subsequent module: JavaScript Deobfuscation, subject to Academy path and availability; Web Requests is already complete. Full Sections 4–5 remain pending.

Follow the [permanent study workflow](../STUDY-WORKFLOW.md). All notes are independently written; original examples are illustrative unless actual evidence is recorded.

Source: [HTB Academy](https://academy.hackthebox.com/app/module/75).

## Section 16 extended research resources

[Eight vulnerability research resource tutorials](Public-Vulnerabilities/Research-Resources/README.md) — free installation where applicable, browser access, data download and terminal examples.
