# Lesson 07/08 — POST

## Learning objectives
Understand POST bodies, form-based login, session cookies, JSON requests, and authenticated request reproduction in an authorized lab.

## GET and POST
GET normally retrieves a representation; POST submits content for processing. GET search parameters are commonly placed in a query string, whereas POST form or JSON data commonly appears in the body. POST can also have query parameters. Bodies accommodate larger payloads and binary uploads, subject to server limits. URL length limits vary across implementations. POST avoids putting body parameters in ordinary URL logs, but applications, proxies and monitoring tools may still log bodies. POST is not encryption; use HTTPS for sensitive information.

## Login forms
Unlike Basic authentication, form-based login commonly submits credentials in a POST body and establishes a session. An illustrative form request:

~~~http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=DEMO_USER&password=DEMO_PASSWORD
~~~

The ampersand separates parameters; the equals sign separates a name and value. cURL -d normally selects POST and defaults to application/x-www-form-urlencoded. -X POST explicitly specifies the method but is usually redundant here. Quote the body in Bash. Use --data-urlencode when values contain characters requiring URL encoding.

~~~bash
curl -i --data-urlencode 'username=DEMO_USER' --data-urlencode 'password=DEMO_PASSWORD' http://127.0.0.1:8080/login
~~~

The example assumes an owned local test service; placeholder credentials are not real. -i includes response headers. A 200 alone does not establish successful login: inspect the response and authenticated access. Some applications redirect after login. -L follows redirects; avoid forcing -X POST with -L unless the protocol requires it, as it may alter expected redirect method behavior. Never send credentials to unintended destinations.

## Session cookies
After a valid login, an application may associate a random session identifier with server-side authenticated state. An illustrative response header:

~~~http
Set-Cookie: PHPSESSID=EXAMPLE_ONLY; path=/
~~~

Set-Cookie is a response header; Cookie is a request header. PHPSESSID is a common default PHP session cookie name, but applications can customize it. A session cookie can authenticate a subsequent request without resending the password. Treat it as sensitive. A session identifier does not prove authentication unless the server associates it with an authenticated session. An anonymous session can also have a cookie. Logout should invalidate the session server-side; a properly invalidated cookie should no longer grant access.

cURL can send a known cookie belonging to your own authorized session with -b, or load cookies from a file. -c saves received cookies to a cookie jar. For a hypothetical local test service, the following illustrates a normal login and session continuation:

~~~bash
umask 077
curl -i -c demo-cookies.txt -d 'username=DEMO_USER&password=DEMO_PASSWORD' http://127.0.0.1:8080/login
curl -i -b demo-cookies.txt http://127.0.0.1:8080/profile
~~~

umask 077 restricts permissions for newly created files in this shell. -c / --cookie-jar saves cookies; -b / --cookie reads a cookie file or sends supplied name=value pairs. Do not commit cookie jars or unsanitized response headers. Remove temporary cookie files when finished. The commands and endpoints are illustrative, not executed. An alternative is explicitly sending a Cookie header using -H; cookie jars are generally preferable for normal session handling.

## Browser DevTools
In Chrome on Windows, open DevTools with Ctrl+Shift+I, select Network, and inspect login or search requests. Headers shows the method and URL, Payload shows form data or JSON, and Response shows the returned content. Application > Storage > Cookies displays cookie attributes for the selected origin. Firefox uses a Storage tab and different keyboard shortcuts. Inspect only your own authorized session. Do not expose session values in screenshots or GitHub.

## JSON POST requests
JSON is a structured data representation using objects, arrays, strings, numbers, booleans and null. A request such as {"search":"london"} is an object with a string property named search. Content-Type: application/json describes the request body format; Accept describes preferred response formats.

Illustrative independent example against an owned local test API:
~~~bash
curl -i -b demo-cookies.txt -H 'Content-Type: application/json' -d '{"search":"Paris"}' http://127.0.0.1:8080/api/cities
~~~

-b loads the session cookie, -H sets the media type, -d sends the JSON body and selects POST, and -i shows response headers. A hypothetical successful response is ["Paris (France)"]. The actual response depends on the application. -d without the JSON Content-Type header defaults to form media type. JSON request bodies are not encrypted merely because they are JSON.

## Fetch API
For an owned same-origin page and supported route:
~~~javascript
fetch('/api/cities', {
  method: 'POST',
  headers: {'Content-Type': 'application/json'},
  credentials: 'same-origin',
  body: JSON.stringify({search: 'Paris'})
}).then(response => {
  if (!response.ok) throw new Error('HTTP ' + response.status);
  return response.json();
}).then(console.log);
~~~
method selects POST; headers specifies the body format; credentials selects same-origin credential handling; JSON.stringify serializes the object; response.json parses a JSON response. Browser cookies are subject to their attributes and browser policies. Review any copied Fetch or cURL command before use; do not paste untrusted code into DevTools.

## Defensive application development
Use TLS, regenerate session identifiers at login, invalidate sessions at logout, and configure Secure, HttpOnly and appropriate SameSite cookie attributes. Enforce authentication and authorization on every protected route. Validate JSON and request media types; use CSRF defenses for cookie-authenticated state-changing operations. A frontend is not a security boundary. In an owned application such as UniTrack these concepts can support safe API testing with disposable records, but no actual UniTrack configuration or tests are claimed here.

## Mandatory HTB exercise
The learner must obtain their own session cookie through valid login with the supplied lab credentials and use cURL to send a JSON POST to the assigned search endpoint. The task specifies the desired search term.

Planned approach:
1. Spawn the assigned lab and obtain its address.
2. Send a valid form login POST and save the issued cookie to a private temporary cookie jar.
3. Inspect the login response for successful authentication, not merely the presence of a cookie.
4. Send an authenticated POST to the specified search endpoint, loading the cookie jar and using application/json with the requested search value.
5. Inspect the response body and identify the exact field or line containing the flag. Submit it privately.
6. Document sanitized observed evidence and the exact answer location after the learner supplies output.

### Observed results and exact evidence location

The learner ran the login request in Ubuntu WSL2. The actual command used -i to include headers, -c to save the session cookie, and -d to submit the form. Sanitized command:

~~~bash
curl -i -c cookies.txt -d 'username=LAB_USER&password=LAB_PASSWORD' http://TARGET_IP:PORT/
~~~

Observed login evidence (session value redacted):

~~~http
HTTP/1.1 200 OK
Set-Cookie: PHPSESSID=[REDACTED]; path=/
Content-Length: 1554
Content-Type: text/html; charset=UTF-8
~~~

The response body contained the City Search title, the logout link and the instruction to type a city name and press Enter. These support successful authentication, rather than relying solely on status 200 or the presence of a cookie.

The learner then loaded the saved cookie jar and submitted the requested search value in JSON. Sanitized command:

~~~bash
curl -b cookies.txt -H 'Content-Type: application/json' -d '{"search":"REQUESTED_TERM"}' http://TARGET_IP:PORT/search.php
~~~

The -b option reads the cookie jar; -H declares the media type; -d provides the JSON body and selects POST by default. The endpoint was specified by the exercise. No manual copying of the session identifier was required.

Actual response structure, with the assessment value redacted:

~~~json
["flag: [FLAG REDACTED]"]
~~~

Exact answer location: response body, first element of the JSON array (index 0), immediately following the literal prefix flag: and a space. Extract only the flag including its braces, excluding the label, quotation marks, array brackets and shell prompt. The pasted command displayed a duplicated fragment at its end, but the returned JSON visibly confirms successful retrieval.

The lab target, live credentials, session cookie and exact assessment answer are omitted from public notes. The learner's output verifies the authenticated request and flag retrieval; platform acceptance has not separately been shown. The cookie jar is sensitive and should be removed after the exercise; do not commit it.

## Verification
Section 7 theory studied and documented. Mandatory login, cookie issuance and authenticated JSON POST flag retrieval verified from learner terminal output. HTB submission acceptance pending. Optional exercises skipped. Original examples are illustrative; sanitized evidence above records actual learner observations. No new lab or application requests were executed by the assistant.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [HTTP state management, RFC 6265](https://www.rfc-editor.org/rfc/rfc6265)
- [JSON, RFC 8259](https://www.rfc-editor.org/rfc/rfc8259)
- [cURL manual](https://curl.se/docs/manpage.html)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [Chrome DevTools Network reference](https://developer.chrome.com/docs/devtools/network/reference/)

Independent English study notes based on the supplied HTB Academy Section 7, with original examples and technical clarifications.
