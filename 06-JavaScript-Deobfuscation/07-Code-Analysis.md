# Section 07 — Code Analysis

**Module:** JavaScript Deobfuscation | **Section:** 7/11 | **Date:** November 26, 2026 (learner local date)

**Status:** Theory documented from the learner-supplied HTB lesson. No mandatory question appears in the supplied text. No POST request, server-side functionality, vulnerability or learner-side installation verified. Previous Section 6 flag recovery was verified; separate submission acceptance was not supplied.

## 1. Code analysis

Code analysis examines source to understand behavior, structure, data flow, dependencies and potential weaknesses. Static analysis examines code without execution; dynamic analysis observes execution in a controlled environment; data flow traces information; control flow traces execution order; security review identifies possible weaknesses; reverse engineering studies existing implementations. This section primarily uses static analysis.

## 2. Supplied function

The relevant non-secret excerpt is:

~~~javascript
'use strict';

function generateSerial() {
    // Additional code omitted
    var xhr = new XMLHttpRequest;
    var url = "/serial.php";
    xhr.open("POST", url, true);
    xhr.send(null);
};
~~~

The function declares an XHR object, assigns a root-relative path, configures an asynchronous POST request and sends it without an explicit body if called. The displayed code does not handle or display the response. That does not prove that the server sends no response. No server request was made in this lesson.

## 3. Strict mode and function declarations

'use strict' enables stricter language rules. Original example:
~~~javascript
'use strict';
total = 100;
console.log(total);
~~~
Expected ReferenceError for undeclared total in an ordinary script. Corrected:
~~~javascript
'use strict';
let total = 100;
console.log(total);
~~~
Expected output: 100. Strict mode is not a security boundary.

A function declaration defines a callable block; the identifier is its name, parentheses contain parameters and braces enclose the body. Example:
~~~javascript
function greet() {
    console.log("Welcome");
}
greet();
~~~
The declaration alone prints nothing; calling greet prints Welcome.

## 4. XMLHttpRequest and AJAX

XMLHttpRequest (XHR) is a built-in browser API for programmatic HTTP communication. It supports text, JSON, HTML, XML and other representations. AJAX means Asynchronous JavaScript and XML; modern AJAX-style applications frequently use JSON. Typical uses include notifications, search suggestions, private messages, dashboard updates and form submissions without full reloads. Browser -> HTTP request -> server processing -> HTTP response -> client response handler.

No separate installation or npm dependency is necessary in a modern browser. Chrome: https://www.google.com/chrome/ ; Firefox: https://www.mozilla.org/firefox/new/ ; standard: https://xhr.spec.whatwg.org/. Node.js does not normally provide native browser XMLHttpRequest; modern Node includes Fetch. See [dedicated XHR guide](Tools/14-XMLHttpRequest.md).

## 5. Variables and URL resolution

var xhr = new XMLHttpRequest creates a request object. var is function-scoped; xhr is a binding; new instantiates the interface. const xhr = new XMLHttpRequest() is a conventional equivalent when the binding is not reassigned. const does not make the object immutable.

var url = "/serial.php" stores a root-relative resource path. In an ordinary document at http://localhost:5000/dashboard, it resolves to http://localhost:5000/serial.php. The scheme, hostname and port are normally retained; a configured document base URL can affect resolution. An absolute URL (https://example.com/serial.php) includes its origin; a root-relative path (/serial.php) starts at the origin root; a path-relative reference (serial.php) resolves relative to the document base directory.

## 6. Request methods

xhr.open("POST", url, true) initializes the request: POST is the method, url the destination, true asynchronous operation. open does not send the request. The asynchronous call permits other JavaScript work while the browser processes network activity. xhr.send(null) initiates the configured request with no explicit body. Cookies and ordinary browser-generated headers may still be present. No explicit headers or response handler appear in the shown function. POST is not inherently secure, nor does an empty body establish a vulnerability.

Conceptual HTTP representation (not a captured request):
~~~http
POST /serial.php HTTP/1.1
Host: localhost:5000
Content-Length: 0
~~~
Actual headers and HTTP version depend on the client and protocol. The example host is an illustrative owned local server; it is not the assigned lab.

GET usually retrieves resources and should be safe; POST submits data or invokes server processing. Server semantics determine what actually happens. A POST may have no body. Response may exist even if code ignores it.

## 7. Response handling and lifecycle

XHR readyState values: 0 UNSENT (created); 1 OPENED (configured); 2 HEADERS_RECEIVED (status/headers available); 3 LOADING (receiving body); 4 DONE (finished, success or failure). status is HTTP status, responseText is text response when responseType permits, onload runs on completed transfer even for HTTP errors, onerror handles network-level failures, ontimeout handles timeout. Check HTTP status independently. See dedicated guide for configuration, troubleshooting and local examples.

Original read-only demonstration against an owned same-origin endpoint, not performed:
~~~javascript
const xhr = new XMLHttpRequest();
xhr.open("GET", "/api/status", true);
xhr.onload = () => {
    if (xhr.status >= 200 && xhr.status < 300) {
        console.log(xhr.responseText);
    } else {
        console.error("HTTP error:", xhr.status);
    }
};
xhr.onerror = () => console.error("Network request failed");
xhr.send(null);
~~~
If the local endpoint returns {"status":"ok"} with HTTP 200, the expected console text is {"status":"ok"}. It requires an actual endpoint and browser page. No execution claimed.

## 8. Hidden or unreleased functionality

HTB suggests the absence of an obvious Generate Serial control may indicate unused or future functionality. That is a hypothesis: a function can be invoked elsewhere, by events or other scripts, and the endpoint may be absent, disabled, restricted or live. Source establishes intended client behavior, not backend availability or a vulnerability.

Professional methodology: identify function, invocation references, method, path, parameters and request body; inspect relevant network evidence if authorized; evaluate server response and access checks only within scope; record observed facts separately from hypotheses. This section does not require sending POST requests; the next HTB section covers HTTP requests. A hidden endpoint is not authorization; an HTTP 200 alone does not demonstrate a vulnerability. Never test unapproved systems.

## 9. Project applications and security

UniTrack: XHR or Fetch could update QR events and visitor dashboards without reload; Flask must validate identity, role, QR status, expiration and entry/exit rules independently. ProTrack-MX: live messages, notifications and grades can use HTTP APIs or Supabase realtime; RLS and backend authorization must protect private records. Hiding a control does not protect an endpoint. Keep privileged keys on the backend, validate input, enforce per-request authorization, apply appropriate CSRF protection for cookie-authenticated state changes, and rate limit sensitive operations. CORS governs browser cross-origin response access, not authorization or CSRF protection. These are architectural recommendations, not new audit findings.

## References

- [HTB JavaScript Deobfuscation](https://academy.hackthebox.com/course/preview/javascript-deobfuscation)
- [MDN XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- [MDN Using XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest_API/Using_XMLHttpRequest)
- [MDN readyState](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/readyState)
- [MDN send](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/send)
- [MDN CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS)
- [WHATWG XMLHttpRequest standard](https://xhr.spec.whatwg.org/)

Next: Section 8 HTTP Requests. No mandatory question in supplied Section 7. No installation, live request, earned points or platform completion claimed.
