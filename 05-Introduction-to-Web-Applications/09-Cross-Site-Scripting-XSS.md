# Lesson 09/17 — Cross-Site Scripting (XSS)

## Learning objectives
Understand browser-side script injection, reflected, stored and DOM-based XSS, browser cookie handling, unsafe rendering sinks and defensive controls.

## What is XSS?
Cross-site scripting occurs when attacker-controlled data is processed as executable content in a trusted website's browser context. HTML injection can alter markup without executing scripts; XSS involves unauthorized script execution. A successful XSS can access data available to scripts in that origin or perform actions as the current user, subject to browser controls and application permissions. XSS does not automatically provide operating-system access.

## Three main types
| Type | Data flow | Example context |
| --- | --- | --- |
| Reflected | Request data is returned in an unsafe response | Search or error page |
| Stored | Input persists and is later rendered unsafely | Comments, posts or profile data |
| DOM-based | Browser code passes untrusted data to an executable sink | Client-side rendering |

The categories can overlap: DOM-based describes a client-side source-to-sink flow, while stored and reflected describe persistence and delivery.

## Sources and sinks
Sources include form inputs, URL data, messages and stored records. Sinks include unsafe HTML rendering or dynamic script evaluation. In the supplied lesson, a prompt value is concatenated into an HTML string and assigned to innerHTML. Event-handler attributes in parsed markup can execute script under appropriate browser conditions; inserting a script element with innerHTML is not generally equivalent to running that script.

## Original local-only demonstration
The following intentionally vulnerable example uses a fixed, harmless message to demonstrate that untrusted markup may trigger an event handler. Use only in an owned local test file. Do not deploy.

~~~html
<input id="message">
<button id="show" type="button">Display</button>
<p id="output"></p>
<script>
  document.getElementById("show").addEventListener("click", () => {
    document.getElementById("output").innerHTML =
      document.getElementById("message").value;
  });
</script>
~~~

A harmless local test input is an image element with a missing local resource and an error handler displaying a fixed demonstration message:

~~~html
<img src="missing-demo-image.png" onerror="alert('Local XSS demo')">
~~~

Expected behavior in a permissive local test context: an alert with the fixed message if the image fails to load. CSP and other controls may prevent execution. No actual test was run.

Replace the unsafe sink for plain text:

~~~javascript
document.getElementById("output").textContent =
  document.getElementById("message").value;
~~~

Now the browser displays the input literally rather than interpreting it as HTML.

## Cookies and security boundaries
A cookie is a browser-managed name-value pair associated with a site and scoped by its attributes. JavaScript's document.cookie interface exposes only cookies available to script in that document. It does not expose HttpOnly cookies. Cookie values are not always session credentials: some contain preferences, synthetic lab markers or other data.

| Attribute | Purpose |
| --- | --- |
| HttpOnly | Prevents direct script access to a cookie |
| Secure | Restricts sending a cookie to secure transport, subject to browser behavior |
| SameSite | Restricts certain cross-site cookie transmission |
| Path and Domain | Define cookie scope; Path is not a strong isolation boundary |

HttpOnly limits direct cookie disclosure but does not prevent XSS from performing same-origin actions using the user's session. Secure and SameSite are not substitutes for eliminating XSS. Browser same-origin policy constrains access to other origins.

## Prevention
- Use textContent for plain text and ordinary escaped framework interpolation.
- Apply context-appropriate output encoding.
- When rich HTML is necessary, use a maintained sanitizer with a restrictive policy.
- Avoid dynamic script evaluation and unsafe HTML sinks.
- Validate data at the appropriate trust boundary; do not rely solely on client-side validation.
- Use a restrictive Content Security Policy as defense in depth.
- Configure session cookies appropriately, including HttpOnly, Secure and SameSite as applicable.
- Keep frameworks, sanitizers and dependencies maintained.
- Test authorized scenarios with harmless markers rather than real user credentials or external collection endpoints.

## Original defensive application example
For a hypothetical school messaging interface, render plain-text messages using escaped interpolation or textContent. Restrict rich text to approved formatting and verify that prohibited markup cannot become executable. Server-side authorization is still required for messages and private records. This is conceptual guidance relevant to school applications, not a confirmed finding in UniTrack or ProTrack-MX.

## Mandatory exercise
The supplied lesson demonstrates displaying the JavaScript-readable cookie value locally in the assigned HTB training page. The exercise requires the actual target-specific value. The learner should use only the assigned lab instance, observe its local demonstration output and submit the value privately. No third-party account, external collection service or real user session is in scope. Do not publish the target address or cookie value.

## Observed practical evidence (sanitized)
The learner opened the assigned HTB instance in Chrome, selected the name-input button, entered the supplied training payload, and confirmed the prompt. The resulting browser alert displayed a cookie name-value pair. This demonstrates script execution and access to a JavaScript-readable lab cookie. To extract the requested value, distinguish the cookie name from the value following the first equals sign. Actual cookie contents and target address are redacted. No external data transfer or session reuse was attempted.

## Verification
Section 9 theory documented; mandatory lab output verified from learner screenshots. Cookie value identified; platform answer acceptance pending. Learner advanced from Section 8; individual acceptance was not separately shown. Full Sections 4–5 remain pending. Original examples are illustrative and unexecuted.

## References
- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [OWASP XSS](https://owasp.org/www-community/attacks/xss/)
- [OWASP XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
- [OWASP DOM-based XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)
- [MDN Document.cookie](https://developer.mozilla.org/en-US/docs/Web/API/Document/cookie)
- [MDN Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie)

Independent English study notes with original examples and technical clarifications.
