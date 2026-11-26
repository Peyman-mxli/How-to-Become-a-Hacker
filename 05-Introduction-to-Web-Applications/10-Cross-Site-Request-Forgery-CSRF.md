# Lesson 10/17 — Cross-Site Request Forgery (CSRF)

## Learning objectives
Understand forged authenticated requests, distinguish CSRF from XSS, identify trust boundaries, and apply layered defenses to state-changing operations.

## What is CSRF?
Cross-site request forgery occurs when an external context causes a user's browser to send an unintended request to a site where the user is authenticated. If the browser includes credentials automatically and the application does not verify request authenticity, the operation may succeed. Authentication establishes identity but does not by itself establish that the user intended a particular action.

The supplied lesson describes a combination of XSS and unauthorized authenticated actions. Classic CSRF does not require script execution in the target origin, whereas XSS executes code in the target site's browser context. XSS can undermine many CSRF protections because malicious code running in the trusted origin may access the same application interfaces as legitimate scripts.

## Typical conditions and impact
- The application accepts automatically attached credentials, such as session cookies.
- A request can cause a state change without adequate request authenticity checks.
- The user has permission to perform that action.

Possible effects include unwanted preference changes, account modifications, or administrative actions. Impact depends on the operation and the authenticated user's permissions. A script reference to an external resource, as discussed in the lesson, would introduce additional risks if an injection vulnerability allowed the browser to execute it. No account takeover payload or external collection endpoint is needed to demonstrate the defensive concept.

## Comparison
| Vulnerability | Main failure | Principal defense |
| --- | --- | --- |
| HTML injection | Untrusted content interpreted as markup | Safe rendering |
| XSS | Untrusted content interpreted as executable code | Contextual output encoding, safe sinks, sanitization when required |
| CSRF | Unintended authenticated request accepted | Anti-CSRF token and request authenticity checks |

## Validation, sanitization and output handling
Validation checks whether data conforms to its expected type, length, format or business constraints. Sanitization removes disallowed content according to a defined policy, especially when rich HTML must be accepted. Output encoding ensures data is interpreted as data in the destination context. Removing all special characters is not a general security solution and can damage legitimate content. Client-side validation improves usability but cannot replace server-side validation.

These controls help prevent injection but do not by themselves prevent CSRF. A forged request can contain perfectly valid input.

## CSRF protection
- Use framework-supported synchronizer tokens for cookie-authenticated state-changing requests. Generate unpredictable tokens, bind them to the session, validate them on the server, and never place them in URLs.
- Alternatively, use a correctly implemented signed double-submit cookie pattern bound to the session where appropriate.
- Use POST, PUT, PATCH or DELETE for state changes, not GET. HTTP method choice alone is insufficient.
- Check Origin and, when appropriate, Referer against an exact trusted origin; define a safe policy for missing headers. Fetch Metadata can supplement this.
- Configure session cookies with SameSite=Lax or Strict as appropriate, Secure over HTTPS and HttpOnly. SameSite concerns site boundaries, not exactly the same concept as origin; HttpOnly prevents direct JavaScript reads, not automatic cookie transmission.
- Require current-password verification or appropriate step-up authentication for sensitive changes. Apply authorization independently of CSRF checks.
- Prevent XSS with safe rendering and context-appropriate output encoding. Use CSP and a WAF as additional layers, not substitutes for application-level fixes.
- Do not assume modern browsers block all unwanted JavaScript or all forged requests. CORS controls access to cross-origin responses; it is not a universal CSRF defense.

## Original defensive Flask example
Illustrative use of Flask-WTF in an owned school application. This is not an installation task or a modification of UniTrack.

~~~python
from flask import Flask, request, jsonify, session, abort
from flask_wtf.csrf import CSRFProtect
import os

app = Flask(__name__)
app.config.update(
    SECRET_KEY=os.environ["SECRET_KEY"],
    SESSION_COOKIE_HTTPONLY=True,
    SESSION_COOKIE_SECURE=True,
    SESSION_COOKIE_SAMESITE="Lax",
)
csrf = CSRFProtect(app)

@app.post("/api/preferences")
def update_preferences():
    if "user_id" not in session:
        abort(401)
    data = request.get_json(silent=True)
    if not isinstance(data, dict):
        abort(400)
    theme = data.get("theme")
    if theme not in ("light", "dark"):
        abort(400)
    # Demo only: a real service would persist the authorized user's preference.
    return jsonify(theme=theme)
~~~

Example Jinja template for a conventional HTML form protected by the same extension:

~~~html
<form method="post" action="/preferences">
  <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
  <button type="submit">Save preferences</button>
</form>
~~~

The form illustrates token transport only; its form endpoint would need to be implemented separately. For JSON requests to /api/preferences, send the server-rendered token using the X-CSRFToken header. The extension validates protected requests before the view runs. Use a secret managed outside the source repository and HTTPS for Secure cookies. Expected behavior: a missing or invalid token is rejected with HTTP 400 by default; a valid token does not bypass authentication or input validation. No server was launched and no requests were executed.

## Original defensive test matrix
| Authorized local scenario | Expected behavior |
| --- | --- |
| Authenticated preference update with valid token and valid data | Accepted |
| Same update with missing token | Rejected |
| Token associated with another session | Rejected |
| Unauthenticated update with valid token for its own session | Rejected |
| Valid token but invalid preference | Rejected |
| GET to state-changing endpoint | No state change |

These are proposed verification cases, not observed results.

## Project connection
UniTrack's cookie-authenticated Flask endpoints should enforce CSRF protection for sensitive actions including password changes and access-control administration. ProTrack-MX's actual authentication transport and Supabase policies must be considered: cookie-based authentication and explicitly attached bearer tokens have different CSRF properties. XSS and server/database authorization remain separate concerns. No repository inspection or changes to either project were performed for this lesson.

## Verification
Section 10 theory studied and documented. No mandatory question appears in the supplied lesson; no lab was performed. Learner advanced from Section 9, whose cookie retrieval was verified but individual platform acceptance was not separately shown. Full Sections 4–5 remain awaiting lesson content.

## References
- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [OWASP CSRF](https://owasp.org/www-community/attacks/csrf)
- [OWASP CSRF Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html)
- [OWASP Input Validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)
- [Flask-WTF CSRF protection](https://flask-wtf.readthedocs.io/en/stable/csrf.html)
- [MDN Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie)

Independent English notes with technical clarifications, original defensive examples and illustrative verification cases.
