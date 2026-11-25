# Lesson 03/08 — HTTP Requests and Responses

## Learning objectives
Identify the fields in HTTP request and response messages, interpret headers and status codes, use cURL verbose output, and inspect authorized requests in browser developer tools.

## HTTP request structure
A client sends a request to a server, which processes the request and returns a response. An illustrative HTTP/1.1 request is:

~~~http
GET /users/login.html HTTP/1.1
Host: example.com
User-Agent: ExampleClient/1.0
Accept: text/html
Cookie: session=example_only

~~~

The request line consists of a method, request target and HTTP version, separated by spaces. GET requests a representation; /users/login.html identifies a path; HTTP/1.1 identifies the protocol version. A query string can follow the path (for example, /products?category=books). Host identifies the intended authority, User-Agent describes client software, Accept identifies acceptable response formats, and Cookie transmits stored cookie data. A blank line terminates the header section. A request may have a body, depending on its semantics and message framing. GET normally has no body.

Technical clarification: HTTP/1.1 message syntax uses CRLF line endings and an empty line after the headers. Content-Length or Transfer-Encoding can determine body framing. Textual HTTP/1.1 syntax is not the same as an unencrypted connection: HTTPS encrypts it in transit. HTTP/2 uses binary frames, HPACK header compression and pseudo-headers rather than the textual start line. HTTP/3 uses QUIC and QPACK. HTTP method names are case-sensitive; header names are case-insensitive.

## HTTP response structure
Illustrative response:
~~~http
HTTP/1.1 200 OK
Content-Type: text/html; charset=UTF-8
Server: ExampleServer/1.0
Set-Cookie: session=example_only; HttpOnly; Secure

<html><body>Example page</body></html>
~~~

The status line contains the HTTP version, numeric status code and optional reason phrase in HTTP/1.1. The headers follow, then a blank line and an optional response body. Content-Type describes the representation; Server optionally identifies server software; Set-Cookie instructs the client to store a cookie according to its attributes. The body may contain HTML, JSON, images, CSS, JavaScript, a document or other data. A HEAD response or a 304 response does not contain a message body.

| Status | Meaning |
| --- | --- |
| 200 | Request succeeded |
| 301 | Permanent redirect |
| 304 | Not modified; cached representation may be reused |
| 401 | Authentication is required or supplied credentials were not accepted |
| 403 | Server refuses the request |
| 404 | Resource not found or not disclosed |

401 and 403 are distinct; neither establishes whether a vulnerability exists. A Server header may be hidden, changed or supplied by a reverse proxy. It is evidence of what the response advertises, not conclusive proof of installed software or patch status.

## cURL verbose output
~~~bash
curl -v http://example.com/
~~~
-v / --verbose displays connection diagnostics and protocol headers, normally on stderr. The response body normally remains on stdout. Illustrative abbreviated output:
~~~text
* Connected to example.com
> GET / HTTP/1.1
> Host: example.com
> Accept: */*
< HTTP/1.1 200 OK
< Content-Type: text/html
< Server: ExampleServer/1.0
~~~
A greater-than prefix identifies outgoing protocol lines, a less-than prefix identifies incoming lines, and an asterisk identifies diagnostics. These are cURL display markers, not bytes in the HTTP header. Verbose mode does not necessarily display every request-body byte. Repeated -v options such as -vvv may increase trace detail on recent cURL versions; older releases may treat repetition differently. Check curl --version and the installed manual.

## Additional original examples
Assume a learner-owned development web server is running on loopback port 5000. No such server was started or tested for this lesson.

~~~bash
curl -i http://127.0.0.1:5000/
curl -v http://127.0.0.1:5000/
curl -sS -D headers.txt -o response.html http://127.0.0.1:5000/
cat headers.txt
~~~
-i includes response headers in the normal output. -v shows outgoing and incoming headers with diagnostic lines. -s suppresses progress, -S retains errors, -D / --dump-header writes response headers to a file, and -o writes the response body to a separate file. All examples use GET by default and assume the endpoint exists. Review the files privately; they may contain authentication data.

To inspect only a server's advertised software header while still making a GET:
~~~bash
curl -sS -D - -o /dev/null http://127.0.0.1:5000/ | grep -i '^server:'
~~~
-D - sends response headers to stdout; -o /dev/null discards the response body on Linux; the pipe passes output to grep; -i makes matching case-insensitive; ^ anchors the match at the beginning of a line. This is an illustrative command against a local owned service. Using curl -I would instead request HEAD, so it should not be substituted when GET is explicitly required.

## Browser developer tools
Chrome and Firefox include developer tools. Press F12 or Ctrl+Shift+I, select Network, and refresh the authorized page. The network panel lists method, status, resource URL, type, timing and other columns depending on browser version. Select a request to inspect its headers, payload (if present), and response. The exact raw/source view labels vary by browser. Filter by URL or resource type to locate a specific request. A browser may make multiple requests for scripts, images, stylesheets and favicon.ico, not just the main document.

The learner uses Chrome and Windows 11; no additional installation is necessary. DevTools and cURL complement each other: DevTools observes browser-driven requests, while cURL makes independently reproducible requests without rendering the page or running JavaScript.

## Security and real-world applications
For an owned Flask application such as UniTrack, inspect HTTP status, response format, redirects and authentication behavior when debugging endpoints. Do not assume a successful status proves authorization is correct. Treat client-supplied headers and parameters as untrusted. Avoid publishing Cookie, Set-Cookie, Authorization, tokens, credentials or full unsanitized network logs. Reduce unnecessary server version disclosure, but maintain actual patches; hiding a banner does not fix vulnerabilities.

## Required HTB questions
The first question asks the learner to identify the method shown in the lesson's intercepted request; the relevant evidence is the first field of the request line. The second asks for the Apache version advertised by the assigned lab server after a GET request. The target address and actual response headers must be obtained before the version is verified. Individual answers and live lab identifiers are omitted from this public portfolio.

## Verification and progress
Section 3 theory studied and documented. Question 1 can be derived from supplied lesson text; platform acceptance not observed. Question 2 awaits target spawn and learner output. Optional verbose and DevTools exercises were not performed. The examples are original illustrations, not captured responses.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [HTTP/1.1, RFC 9112](https://www.rfc-editor.org/rfc/rfc9112)
- [HTTP/2, RFC 9113](https://www.rfc-editor.org/rfc/rfc9113)
- [cURL manual](https://curl.se/docs/manpage.html)
- [Chrome DevTools Network reference](https://developer.chrome.com/docs/devtools/network/reference/)
- [Firefox Network Monitor](https://firefox-source-docs.mozilla.org/devtools-user/network_monitor/)

Independent English study notes based on the supplied HTB Academy lesson, with original examples and technical clarifications.
