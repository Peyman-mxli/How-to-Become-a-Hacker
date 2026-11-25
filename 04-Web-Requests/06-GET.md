# Lesson 06/08 — GET

## Learning objectives
Understand GET query parameters, HTTP Basic authentication, authentication headers, and how to reproduce an authorized browser request using cURL or Fetch.

## GET and browser activity
A navigation normally starts with a GET request for a document. Scripts can subsequently request additional resources using Fetch or XMLHttpRequest. The Network panel reveals the actual requests and responses, including the method, URL, status, initiator, and returned data. Do not assume visible page content is the full response.

Example request:
~~~http
GET /search?term=London HTTP/1.1
Host: example.com
Accept: application/json

~~~
The path identifies the endpoint; the query string supplies input. Query parameters are common with GET, but are not exclusive to GET. A GET request body has no generally defined semantics. URL query data can appear in logs, history, or referrer information; never place secrets in a query string.

## HTTP Basic authentication
Basic authentication is a standardized HTTP authentication scheme (RFC 7617). It may be enforced by the server, proxy, or application. An unauthenticated request to a protected resource may return:
~~~http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm="Demo"
~~~
401 indicates that valid credentials are required. WWW-Authenticate provides the authentication challenge. Basic identifies the scheme, and realm names the protection space. A subsequent request supplies Authorization with the Base64 encoding of username:password. Valid credentials may permit a 200 response, depending on the resource and access policy.

Basic authentication sends reusable credentials in a reversibly encoded form. Base64 is neither encryption nor hashing. Protect Basic authentication with HTTPS outside isolated authorized training labs. Do not reuse lab credentials on real systems.

## cURL authentication options
The following templates refer to an owned, hypothetical service. USER is a placeholder; enter the password when prompted.
~~~bash
curl -i http://127.0.0.1:8080/
curl -i -u USER http://127.0.0.1:8080/
curl -v -u USER http://127.0.0.1:8080/
~~~
-i includes response headers, -u or --user supplies a username and optionally a password, and -v shows verbose request/response diagnostics. With only the username, cURL prompts for the password. Basic is the default HTTP authentication scheme when -u is used unless another scheme is selected. A password included in a command may appear in shell history or process arguments; verbose logs can expose authorization headers. Do not publish them.

A historical URL userinfo form is http://USER:PASSWORD@HOST:PORT/. cURL can accept it, but modern browsers may reject it or restrict its use. Avoid this practice because URLs can expose credentials.

## Authorization header and Base64
The Basic header has the structure:
~~~http
Authorization: Basic BASE64_OF_USERNAME_COLON_PASSWORD
~~~
For a harmless independent illustration, encode the literal string demo:example in Ubuntu:
~~~bash
printf '%s' 'demo:example' | base64
~~~
Illustrative expected output: ZGVtbzpleGFtcGxl
~~~bash
printf '%s' 'ZGVtbzpleGFtcGxl' | base64 -d
~~~
Illustrative expected output: demo:example
printf with %s writes the string without adding a newline, the pipe forwards bytes to base64, and -d decodes them. The strings are invented examples, not HTB credentials. The ability to reverse Base64 explains why HTTPS is necessary. The encoded value can be supplied using curl -H 'Authorization: Basic BASE64_VALUE' for a known authorized test service, but -u is preferable for ordinary use. Never share a real header or token.

Technical clarification: Bearer is an HTTP authorization scheme used with many token formats, including JWTs. A JWT is not necessarily encrypted: many JWTs are signed JWS tokens whose payload is readable. Encrypted JWTs use JWE. A valid signature and correct claim verification are important; HTTPS remains necessary. Authentication establishes identity, while authorization determines allowed actions.

## Query parameters and cURL
A browser search may send a request to a separate backend resource with a query parameter. Inspect the actual Request URL in DevTools rather than assuming how the application works.
~~~bash
curl -u USER 'http://127.0.0.1:8080/api/cities?search=London'
curl -u USER -G --data-urlencode 'search=New York' http://127.0.0.1:8080/api/cities
~~~
The first command explicitly includes a query string. -G (or --get) places supplied data into the URL query and uses GET. --data-urlencode encodes the input safely for use as a parameter. Quoting URLs prevents shell metacharacters such as ampersands from being interpreted by Bash. The endpoint is hypothetical and must exist before these examples can work.

## DevTools: Copy as cURL and Fetch
In Chrome on Windows, press Ctrl+Shift+I (or right-click and Inspect), select Network, and optionally use Fetch/XHR. Clear old requests if helpful, perform the authorized search, then select the relevant row. In Headers, record the Request URL, Request Method, and response status. In Payload, Chrome may show decoded query string parameters. In Response, inspect the raw returned data.

Right-click the relevant request and choose Copy > Copy as cURL (bash), if available. Use the Bash version in Ubuntu WSL2. The copied command may include credentials, cookies, and incidental headers: inspect it before running or sharing. Keep only the fields the authorized application requires. Do not copy a command from an unrelated or untrusted source.

The browser can also copy an authorized request as Fetch. In Chrome, use the Console tab (Ctrl+Shift+J opens the Console; Firefox shortcuts differ). Review code before running it; do not bypass browser self-XSS protections to paste untrusted code.

Independent same-origin example, assuming an owned local page and matching endpoint:
~~~javascript
fetch('/api/cities?search=London')
  .then(response => {
    if (!response.ok) throw new Error('HTTP ' + response.status);
    return response.text();
  })
  .then(console.log);
~~~
fetch sends GET by default. response.ok indicates a status in the 200–299 range; response.text reads the body; console.log prints it. Browser credential and CORS policies still apply. This code has not been executed.

## Defensive software development
For a hypothetical UniTrack search API, enforce server-side authorization on every request, validate search parameters, parameterize database queries, and return only records the requester may access. Use HTTPS and avoid putting sensitive student data or credentials into query strings. UI filtering is not an authorization control. An incorrect UI result does not by itself demonstrate a security vulnerability.

## Mandatory HTB exercise
The question requires examining the browser's search request and using cURL to reproduce it with the supplied authorized lab credentials and the requested search term.

Planned approach:
1. Spawn the assigned target and open its URL in Chrome.
2. Authenticate using the credentials provided privately by the lesson.
3. Open DevTools Network, select Fetch/XHR, and perform an ordinary search.
4. Select the resulting request and examine Headers > Request URL and Request Method, and Payload > Query String Parameters if available.
5. Reproduce that verified request in Ubuntu WSL2 with cURL, changing only the search value as requested by the exercise. Supply the lab authentication credentials.
6. Inspect the returned response for the flag, extract its exact value, and submit privately.

Evidence location: the actual Network row and its Request URL/Method establish the request; the terminal response body establishes the answer. The lesson shows an example endpoint, but the assigned target, actual response, and answer have not yet been observed. No lab credentials, exact flags, or live lab addresses are published.

## Verification
Section 6 theory studied and documented. Mandatory exercise pending target spawn, browser request inspection, and cURL output. Optional exercises skipped. All examples above are illustrative; no new requests have been executed.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [Basic authentication, RFC 7617](https://www.rfc-editor.org/rfc/rfc7617)
- [JSON Web Token, RFC 7519](https://www.rfc-editor.org/rfc/rfc7519)
- [cURL manual](https://curl.se/docs/manpage.html)
- [Chrome DevTools Network reference](https://developer.chrome.com/docs/devtools/network/reference/)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

Independent English study notes based on the supplied HTB Academy Section 6, with technical clarifications and original examples.
