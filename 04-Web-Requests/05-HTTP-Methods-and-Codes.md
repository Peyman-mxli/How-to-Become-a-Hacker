# Lesson 05/08 — HTTP Methods and Codes

## Learning objectives
Identify the purpose of common HTTP methods, interpret the five status-code classes, and understand their relevance to REST APIs, debugging and authorized security assessment.

## Request methods
An HTTP method describes the semantics requested by the client. Method names are case-sensitive. The request line in HTTP/1.1 begins with the method, followed by the target and protocol version. Browser Network tools show it in the Method column or the request details.

| Method | Purpose | Typical use |
| --- | --- | --- |
| GET | Retrieve a representation | Read a product or record |
| POST | Process a submitted representation according to resource semantics | Submit a form or create a server-assigned resource |
| HEAD | Retrieve the headers corresponding to GET without a response body | Inspect metadata before download |
| PUT | Create or replace the state of the specified resource | Replace a known record |
| DELETE | Remove the association between a URI and its current functionality | Delete a record |
| OPTIONS | Describe communication options | Discover advertised methods or perform a CORS preflight |
| PATCH | Apply a partial modification | Update selected fields |

The application determines which methods are supported. OPTIONS and Allow are useful signals but do not prove that a method is authorized for a particular user. HEAD may omit headers that are only determined while generating the GET body. DELETE does not necessarily physically erase stored bytes. POST does not inherently create a resource. PUT replaces or creates the targeted state; PATCH changes part of it using an application-supported patch format.

## Safe and idempotent semantics
A safe method has read-only intended semantics, although logging and other incidental side effects may occur. An idempotent method has the same intended effect when an identical request is repeated as when it is sent once.

| Method | Safe | Idempotent |
| --- | --- | --- |
| GET | Yes | Yes |
| HEAD | Yes | Yes |
| OPTIONS | Yes | Yes |
| PUT | No | Yes |
| DELETE | No | Yes |
| POST | No | Not guaranteed |
| PATCH | No | Not guaranteed |

A repeated DELETE may yield different response codes without changing its idempotent intended effect. Never assume an application implements method semantics correctly.

## Status-code classes
| Class | Meaning |
| --- | --- |
| 1xx | Informational or interim response |
| 2xx | Successful processing |
| 3xx | Redirection or related cache handling |
| 4xx | Client-error classification |
| 5xx | Server-error classification |

A 4xx response does not necessarily mean malformed syntax; authentication, authorization and unavailable resources can also produce this class. A 3xx response does not always redirect to a new URL (304 is a cache validation response).

| Code | Meaning |
| --- | --- |
| 100 Continue | Client may continue sending request content |
| 200 OK | Successful request; exact response semantics depend on method |
| 201 Created | One or more resources created |
| 204 No Content | Successful response without content |
| 301 Moved Permanently | Permanent redirect |
| 302 Found | Temporary redirection |
| 304 Not Modified | Cached representation can be reused |
| 400 Bad Request | Request cannot be processed due to perceived client error |
| 401 Unauthorized | Valid authentication credentials are required |
| 403 Forbidden | Server understood request but refuses it |
| 404 Not Found | Resource not found or existence not disclosed |
| 405 Method Not Allowed | Method known but not supported by this resource; Allow is required |
| 409 Conflict | Conflict with current resource state |
| 415 Unsupported Media Type | Unsupported request representation format |
| 429 Too Many Requests | Rate limit exceeded |
| 500 Internal Server Error | Unexpected server-side failure |
| 502 Bad Gateway | Invalid upstream response at a gateway |
| 503 Service Unavailable | Temporary inability to handle request |
| 504 Gateway Timeout | Upstream response timed out |

The supplied lesson emphasizes 200, 302, 400, 403, 404 and 500. The other entries above are supplemental. Providers may define nonstandard codes; consult their own documentation. A status code is evidence of reported processing outcome, not proof of a vulnerability or access-control correctness.

## cURL and method selection
The examples below assume an owned, disposable local API at 127.0.0.1:5000 with a fictional /api/demo-records route. They were not executed and do not establish that UniTrack implements these endpoints. Write operations are only appropriate for explicitly authorized disposable test data.

~~~bash
curl -i http://127.0.0.1:5000/api/demo-records/1
curl -I http://127.0.0.1:5000/api/demo-records/1
curl -i -X OPTIONS http://127.0.0.1:5000/api/demo-records/1
~~~
GET is the default when no options select another method. -i includes response headers; -I makes a HEAD request; -X selects a named method. OPTIONS may return Allow, but behavior depends on the application.

Illustrative create and update operations:
~~~bash
curl -i -H 'Content-Type: application/json' -d '{"name":"Demo"}' http://127.0.0.1:5000/api/demo-records
curl -i -X PUT -H 'Content-Type: application/json' -d '{"name":"Updated","active":true}' http://127.0.0.1:5000/api/demo-records/1
curl -i -X PATCH -H 'Content-Type: application/json' -d '{"active":false}' http://127.0.0.1:5000/api/demo-records/1
curl -i -X DELETE http://127.0.0.1:5000/api/demo-records/1
~~~
-H sets a header; Content-Type identifies the representation; -d supplies the request body and normally selects POST unless another method is specified. The hypothetical PATCH endpoint accepts an application-defined JSON partial update. Other APIs may require application/merge-patch+json or application/json-patch+json. DELETE changes state; never run against real records without explicit authorization. These examples use Bash syntax in Ubuntu WSL2, not PowerShell quoting.

## Additional original example: inspecting response codes
~~~bash
curl -sS -o /dev/null -w 'HTTP status: %{http_code}\n' http://127.0.0.1:5000/
~~~
-s suppresses progress, -S preserves errors, -o /dev/null discards the body, and -w formats selected transfer information. %{http_code} is the final HTTP response code. The backslash-n prints a newline. An illustrative output is HTTP status: 200. A connection failure may yield 000 in this format, which is not an HTTP status code received from a server. cURL normally does not treat HTTP 4xx or 5xx as a transfer failure unless an appropriate fail option is used.

## REST API and defensive development
CRUD means Create, Read, Update, Delete. A common REST mapping is POST, GET, PUT/PATCH, DELETE, respectively; actual API contracts vary. In a system such as UniTrack, a hypothetical student-record endpoint should enforce authentication, role and object-level authorization on every method. A user who can read a record is not automatically authorized to edit or delete it. Validate schemas and media types, prevent unintended state changes via GET, use appropriate CSRF protections for cookie-authenticated operations, and avoid sensitive information in URLs or error bodies. Use HTTPS for both GET and POST. Do not infer that a 403, 404 or 200 alone proves correct security behavior. No new tests of UniTrack were performed.

## Verification
Section 5 theory studied and documented. No mandatory question appears in the supplied section. All commands and outputs are illustrative; no API or lab commands were executed. Platform completion is not separately confirmed.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [HTTP PATCH, RFC 5789](https://www.rfc-editor.org/rfc/rfc5789)
- [MDN HTTP request methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- [MDN HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [cURL manual](https://curl.se/docs/manpage.html)

Independent English study notes based on the supplied HTB Academy Section 5, with original examples and technical clarifications.
