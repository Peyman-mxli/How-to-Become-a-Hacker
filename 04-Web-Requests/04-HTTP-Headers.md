# Lesson 04/08 — HTTP Headers

## Learning objectives
Understand HTTP field syntax, the traditional header categories, authentication and session fields, browser security policies, cURL options, and the use of browser DevTools to inspect requests made after initial page load.

## HTTP fields
A field has a name and value, separated by a colon in HTTP/1.x textual syntax. Header names are case-insensitive. Some fields accept lists; others, notably Set-Cookie, require separate handling. The lesson uses general, entity, request, response and security headers as a learning model. RFC 9110 organizes fields by function rather than these historical categories. Consult the IANA registry for authoritative definitions.

## General headers
| Field | Illustrative value | Function |
| --- | --- | --- |
| Date | Wed, 16 Feb 2022 10:38:44 GMT | Message origination time |
| Connection | close | HTTP/1.x hop-by-hop connection handling; persistent connections are default in HTTP/1.1 |
| Cache-Control | no-store | Directs caches not to store the message |

Connection-specific fields are prohibited in HTTP/2 and HTTP/3. Sensitive responses should use appropriate cache controls; no-store does not erase previously cached data or prevent every possible local record.

## Entity, representation and framing fields
| Field | Illustrative value | Function |
| --- | --- | --- |
| Content-Type | application/json | Media type; optional charset describes text encoding |
| Content-Length | 15 | Content length in bytes, when applicable |
| Content-Encoding | gzip | Encoding applied to representation |
| Transfer-Encoding | chunked | HTTP/1.1 transfer framing |
| Content-Type boundary parameter | multipart/form-data; boundary=example123 | Identifies delimiters between multipart sections |

Content-Length is not mandatory for every message. Chunked framing permits transfer without an upfront length. Transfer-Encoding is not used for chunked framing in HTTP/2 or HTTP/3. Inconsistent interpretation of framing between proxies and backends can cause request smuggling; defensively reject ambiguous framing and keep intermediaries patched. Compression is not encryption.

Illustrative JSON response body (15 bytes with no trailing newline):
~~~http
Content-Type: application/json
Content-Length: 15

{"status":"ok"}
~~~

## Request headers
| Field | Illustrative value | Function |
| --- | --- | --- |
| Host | example.com | Identifies intended authority; virtual host routing |
| User-Agent | ExampleClient/1.0 | Describes client software |
| Referer | https://example.com/ | Referring resource, subject to referrer policy |
| Accept | application/json | Desired response media type; */* accepts any |
| Cookie | theme=dark; session=example_only | Sends stored cookies; pairs separated by semicolons |
| Authorization | Bearer EXAMPLE_TOKEN | Carries credentials under an authentication scheme |
| X-Forwarded-For | 203.0.113.5 | Conventional proxy forwarding metadata |

Referer is the standardized historical spelling. Client-supplied fields can be fabricated and are not trustworthy proof of identity. Validate Host against expected authorities; trust forwarding metadata only from explicitly configured proxies that sanitize incoming values. Basic authentication uses Base64 encoding, not encryption; Bearer credentials must be protected; Digest is another authentication scheme. Browsers may manage HTTP authentication automatically. Never publish actual cookies or authorization values.

## Response headers
| Field | Illustrative value | Function |
| --- | --- | --- |
| Server | ExampleServer/1.0 | Advertises server software, not conclusive proof of actual version |
| Location | /login | Redirect destination or newly created resource URI, depending on status |
| Set-Cookie | session=example_only; Secure; HttpOnly; SameSite=Lax | Directs client to store a cookie with attributes |
| WWW-Authenticate | Basic realm="Example" | Authentication challenge, required with a 401 response |

Secure limits cookie transmission to secure contexts (subject to browser rules); HttpOnly limits script access to the cookie, but does not eliminate XSS; SameSite restricts cross-site cookie transmission. Strict and Lax impose different restrictions; None permits cross-site use and generally requires Secure. Validate redirect destinations to avoid open redirects. Keep server software patched even if version disclosure is reduced.

## Security headers
| Field | Illustrative value | Purpose |
| --- | --- | --- |
| Content-Security-Policy | script-src 'self' | Restricts permitted scripts; defense in depth against XSS |
| Strict-Transport-Security | max-age=31536000 | Instructs supporting browsers to use HTTPS for the host for one year |
| X-Content-Type-Options | nosniff | Prevents MIME sniffing in applicable browser contexts, notably scripts and styles |
| X-Frame-Options | DENY | Prevents framing; clickjacking defense |
| Access-Control-Allow-Origin | https://app.example.com | Allows specified browser origin to read a cross-origin response under CORS |
| Referrer-Policy | origin | Sends only the origin as referrer when applicable |

CSP must be designed for the application; it does not replace output encoding. HSTS is accepted over valid HTTPS and must be deployed carefully. CSP frame-ancestors is the modern framing control. CORS is not authentication or server-side authorization. A browser rejects wildcard Access-Control-Allow-Origin for credentialed requests; unsafe arbitrary origin reflection with credentials is a distinct risk. SameSite is defense in depth and does not replace CSRF protection. Referrer-Policy controls what referring URL information is sent.

## cURL options
~~~bash
curl -I https://example.com/
curl -i https://example.com/
curl -v https://example.com/
curl -A 'ExampleClient/1.0' https://example.com/
curl -H 'Accept: application/json' https://example.com/
~~~
-I sends HEAD and displays response headers. -i includes response headers and normally also the body for a GET. -v displays protocol diagnostics, including outgoing and incoming headers. -A sets User-Agent; -H specifies a custom request header. Options are case-sensitive. The lesson's combined header listing is illustrative, not literal output of curl -I. A real HEAD response does not print outgoing request headers unless additional diagnostics are requested. User-Agent changes do not constitute proof of browser identity.

## Additional original example: local API debugging
Assume an owned Flask test server is already running on loopback and implements a hypothetical endpoint /api/health:
~~~bash
curl -i -H 'Accept: application/json' http://127.0.0.1:5000/api/health
curl -v -A 'LocalTrainingClient/1.0' http://127.0.0.1:5000/api/health
~~~
The first command requests JSON and includes response headers. The second sends a customized User-Agent and displays it among outgoing headers. Illustrative expected response is 200 OK with Content-Type: application/json if the route exists and returns JSON. No local test was performed.

For a production deployment of an owned application such as UniTrack, sensitive responses should use appropriate cache controls, session cookies should have suitable attributes, and CSP, framing policy and HSTS should be configured and tested. CORS should allow only intended origins. These recommendations do not establish the application's current configuration.

## Browser DevTools
Chrome and Firefox include Network inspection. In Chrome, open the assigned page, press F12 or Ctrl+Shift+I, choose Network, and reload the page while recording. Each row corresponds to a network request. Select a row and inspect Headers for Request URL, Request Method and Status Code, and Response for the returned content. Preview may display a parsed representation. Fetch/XHR filtering can reveal asynchronous API calls; if absent, return to All because a flag resource may use a different request type. Cookies can be inspected in relevant request details, but do not publish sensitive values.

## Required HTB exercise
The supplied question asks the learner to locate the request for a flag loaded after the initial page. The appropriate tool is browser DevTools Network, not merely viewing the initial HTML source.

Planned procedure:
1. Spawn the assigned HTB target and open its assigned address in Chrome.
2. Open DevTools Network before reloading. Ensure recording is enabled; reload the page.
3. Inspect requests made after the initial document. Use Fetch/XHR as a starting filter, then All if needed.
4. Select the relevant request and inspect Headers for the exact Request URL, method and status, then Response for the returned content.
5. Extract the flag from the actual response and submit it privately to HTB.

### Observed practical results and exact evidence location
The learner spawned the assigned lab and opened it in Chrome on Windows 11. F12 opened the Windows projection menu on the learner's keyboard, so Ctrl+Shift+I was used to access DevTools instead. After opening Network and reloading, the learner selected Fetch/XHR.

The first screenshot showed a request whose name began with flag_, status 200, type fetch, and initiator script.js:7. The request name was truncated in the screenshot. Status 200 indicated a successful response; fetch identified the browser request type; script.js:7 identified the initiating script location. These observations identified the relevant row without guessing the endpoint.

The learner selected that row and opened Response. The second screenshot displayed the flag on line 1 of the response body. Exact evidence location: Chrome DevTools > Network > Fetch/XHR > flag request > Response > line 1. Copy the entire returned flag, including its surrounding braces, into the private HTB answer box. The line number is a UI label, not part of the answer. The exact flag and resource identifier are redacted here, and the full Request URL was not inspected in the supplied screenshots.

Sanitized observed evidence:
~~~text
Name: flag_[REDACTED]
Status: 200
Type: fetch
Initiator: script.js:7
Response line 1: [FLAG REDACTED]
~~~
This is actual learner evidence with sensitive assessment details removed, not an illustrative result. No cURL command was needed. The browser can receive additional resources after rendering the initial page; inspecting the initial HTML alone would not necessarily reveal this response. For defensive development, never send secrets to a browser that is not authorized to receive them.

## Verification
Section 4 theory studied and documented. Required Network request identification and flag extraction verified from learner screenshots. HTB submission acceptance and section completion not separately confirmed. Optional recall and cURL exercises were skipped. Original command examples are illustrative; the sanitized walkthrough records actual observations.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [HTTP/1.1, RFC 9112](https://www.rfc-editor.org/rfc/rfc9112)
- [IANA HTTP Field Name Registry](https://www.iana.org/assignments/http-fields/http-fields.xhtml)
- [OWASP Secure Headers Project](https://owasp.org/www-project-secure-headers/)
- [cURL manual](https://curl.se/docs/manpage.html)
- [Chrome DevTools Network reference](https://developer.chrome.com/docs/devtools/network/reference/)

Independent English study notes based on the learner-supplied HTB Academy Section 4, with technical clarifications and original examples.
