# Lesson 07/17 — Sensitive Data Exposure

## Learning objectives
Identify confidentiality risks in client-delivered resources, distinguish page source from the live DOM and backend source, inspect authorized lab responses, and prevent unintended exposure.

## Sensitive data exposure
Information is exposed when it becomes accessible outside its intended authorization boundary. Examples include credentials, private API keys, session tokens, password hashes, personal data, internal configuration and diagnostic output. A public endpoint name is not automatically a vulnerability: sensitivity, intended access and demonstrated impact matter.

Frontend vulnerabilities can affect users, including privileged administrators. An issue in the browser can enable unauthorized actions or disclose protected data if additional controls fail. It is inaccurate to assume that frontend vulnerabilities cannot produce permanent damage.

## Where exposure can occur
HTML source includes markup and comments sent to the browser. The live DOM can differ from the initial response because scripts modify the document. External JavaScript bundles, source maps, API responses, hidden elements and client storage may contain data. Server-side source is not normally delivered to the client.

Disabling right-click or hiding an element with CSS does not create confidentiality or authorization.

| Tool | Purpose |
| --- | --- |
| View Page Source (Ctrl+U in Chrome on Windows) | Review initial HTML delivered to the browser |
| Find (Ctrl+F) | Locate text in the displayed source |
| DevTools Elements | Inspect current DOM |
| DevTools Network | Inspect resource requests and responses |
| cURL | Retrieve an authorized HTTP response |
| Web proxy | Inspect permitted HTTP traffic |

## Original example: accidental disclosure in an HTML comment
Consider a fictional application that mistakenly includes a synthetic placeholder in its rendered source:

~~~html
<form method="post" action="/login">
  <label for="username">Username</label>
  <input id="username" name="username" required>
  <label for="password">Password</label>
  <input id="password" name="password" type="password" required>
  <!-- Development note: sample credential PLACEHOLDER_ONLY -->
  <button type="submit">Sign in</button>
</form>
~~~

The comment is not rendered as normal page text but is delivered in the HTML response. The placeholder is fictional and is not a usable credential. Removing a real exposed credential from the source does not invalidate copies already obtained: revoke or rotate the credential, review access logs and investigate the affected environment.

## Prevention
- Classify data and send only information required for authorized client operations.
- Keep privileged credentials and signing keys in server-side secret stores.
- Review rendered responses, production bundles, configuration and source maps before release.
- Avoid shipping debugging comments with secrets or sensitive private data.
- Apply server-side role and object authorization to protected endpoints and data.
- Use HTTPS to protect data in transit; TLS does not prevent a recipient from inspecting their own response.
- Use secret scanning on owned repositories and deployment artifacts; review results manually.
- Revoke exposed credentials and verify remediation.
- Treat minification and obfuscation as code-distribution measures, not security boundaries. They do not reliably conceal secrets delivered to a browser.

## Additional original example: school application
A hypothetical school dashboard should return only the records the authenticated user is authorized to see. A private database service credential must stay on the server. A browser-facing public client key may be intentionally public if access is constrained by appropriately enforced backend policies. Public configuration identifiers should not be confused with privileged credentials.

## Mandatory lab
The supplied lesson includes a required exercise asking the learner to inspect a login form for an exposed password. The learner inspected the assigned HTB training instance from Ubuntu WSL2 using cURL. The returned HTML contained an exposed credential pair in a developer comment immediately after the password input and before the login button.

### Observed practical evidence (sanitized)

~~~bash
curl -s http://TARGET_IP:PORT/
~~~

The -s option suppresses the progress meter; GET is the default HTTP method. The response body contained the following structure, with the actual credential pair redacted:

~~~html
<input type="password" required>
<!-- Developer reminder to remove test credentials [USERNAME REDACTED]:[PASSWORD REDACTED] -->
<button type="submit">Login</button>
~~~

The password is the value after the colon separating the username and password. Only the password is requested. The learner inspected the raw source directly; no login attempt or credential validity test was performed. No HTTP status code was recorded because response headers were not requested. The target address and actual credential values are not published.

## Verification
Theory documented. Mandatory practical evidence verified from the learner's terminal output; exposed password identified in the HTML comment. HTB answer acceptance and section completion remain pending. Section 6 question area was not supplied; the learner has advanced to Section 7. Full Sections 4–5 remain pending. All original examples are illustrative.

## References
- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [OWASP WSTG — Review Webpage Content for Information Leakage](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/01-Information_Gathering/05-Review_Webpage_Content_for_Information_Leakage)
- [OWASP Secrets Management](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html)
- [MDN HTML comments](https://developer.mozilla.org/en-US/docs/Web/HTML/Guides/Comments)

Independent English study notes with original examples and clearly identified technical clarifications.
