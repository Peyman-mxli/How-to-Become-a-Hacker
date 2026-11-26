# Section 14 — Development Frameworks & APIs

**Provider:** Hack The Box Academy  
**Status:** Theory documented; mandatory assigned-target exercise pending.

## 1. Development frameworks

A framework supplies reusable application structure, routing, request/response abstractions, middleware, validation and other facilities. Full-stack frameworks may also include templates, database migrations, authentication and object-relational mapping (ORM). Authentication establishes identity; authorization determines permitted operations. An ORM maps application objects to database records, but unsafe raw queries can still introduce injection vulnerabilities.

| Framework | Language/runtime | Common characteristics |
| --- | --- | --- |
| Laravel | PHP | Routing, Eloquent ORM, Blade templates, Artisan commands |
| Express | JavaScript / Node.js | Minimal routing and middleware |
| Django | Python | ORM, templates, admin, auth, migrations |
| Ruby on Rails | Ruby | Convention over configuration, Active Record, MVC |
| Flask (supplement) | Python | Lightweight routing, extensions, Jinja templates |

All five have open-source licenses. Company examples in the course are historical illustrations, not verified current deployments. See [individual framework guides](Development-Frameworks/README.md).

A framework is not a language, runtime, web server or database. MVC (Model-View-Controller) separates data/domain logic, presentation and request coordination. Django commonly calls its arrangement model-template-view (MTV); its view handles request logic.

## 2. APIs and request flow

API means Application Programming Interface: a contract by which software calls functionality or exchanges data. APIs may be local library interfaces or network services. Web APIs commonly use HTTP. An endpoint is an address and operation exposed to callers; a resource is a concept identified by a URI; a representation is its transmitted form (for example JSON). A client sends a request, a server routes it, validates identity and permission, performs the operation and returns status, headers and optionally content.

```text
Browser -> HTTP request -> router -> handler -> database/service
Browser <- HTTP response <---------------------------+
```

## 3. HTTP parameters

| Location | Example | Purpose |
| --- | --- | --- |
| Query string | /search?item=apples&limit=5 | Search, filter, pagination |
| Path | /api/notices/42 | Resource identifier |
| Form body | item=apples | Form fields |
| JSON body | {"item":"apples"} | Structured request content |
| Header | Accept: application/json | Metadata/content negotiation |

The query begins after ?; & separates fields; = separates name/value; percent encoding represents reserved characters. Query strings can accompany methods other than GET. POST bodies are not query strings. HTTPS protects the transfer in transit, but URLs can appear in logs/history; never put passwords or tokens in query parameters.

Illustrative local requests (require a matching owned server):
```bash
curl -G 'http://127.0.0.1:5000/search' --data-urlencode 'item=green apples'
curl -d 'item=apples' 'http://127.0.0.1:5000/search'
curl -H 'Content-Type: application/json' -d '{"item":"apples"}' 'http://127.0.0.1:5000/api/search'
```
-G puts supplied data in a GET query; --data-urlencode encodes values; -d supplies request content and implies POST unless overridden; -H sets a header. Default -d media type is application/x-www-form-urlencoded. These are examples, not lab results.

## 4. SOAP

SOAP 1.1 historically expands to Simple Object Access Protocol; SOAP 1.2 is named SOAP. The course's expansion and example namespace are introductory inaccuracies. SOAP is an XML messaging protocol, often carried over HTTP, but not restricted to HTTP. The Envelope identifies a SOAP message; Header is optional metadata; Body contains the operation payload; Fault reports an error. WSDL (Web Services Description Language) can describe service operations, bindings and messages; XSD describes XML structure and types. SOAP does not inherently require stateful sessions or serialized language-specific objects.

Original SOAP 1.2 request body:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<env:Envelope xmlns:env="http://www.w3.org/2003/05/soap-envelope"
              xmlns:sch="urn:school-demo">
  <env:Body>
    <sch:GetNotice><sch:Id>42</sch:Id></sch:GetNotice>
  </env:Body>
</env:Envelope>
```
env is a namespace prefix; xmlns associates it with the SOAP namespace; sch identifies the illustrative school service namespace; GetNotice and Id are application-defined. The HTTP Content-Type for SOAP 1.2 is application/soap+xml. SOAP 1.1 uses a different namespace and commonly text/xml plus SOAPAction. Binary data may be encoded or conveyed through attachment mechanisms. Protect XML parsing against external entities, DTD processing, excessive nesting and oversized messages; use authenticated transport and authorization.

## 5. REST

REST (Representational State Transfer) is an architectural style, not an XML/JSON wire protocol. Its constraints include client/server separation, stateless interactions, cacheability, uniform interface, layered architecture and optional code on demand. Uniform interface includes resource identification, representations, self-descriptive messages and hypermedia-driven application state. Many practical HTTP JSON APIs implement only parts of this style. REST can use query parameters as well as paths, and formats besides JSON.

Original resource-oriented API:
| Method | Route | Intended purpose | Typical status |
| --- | --- | --- | --- |
| GET | /api/notices/42 | Retrieve | 200 |
| GET | /api/notices?group=1A | Filter collection | 200 |
| POST | /api/notices | Create or other resource-specific processing | 201 |
| PUT | /api/notices/42 | Create or replace at known URI | 200/201/204 |
| PATCH | /api/notices/42 | Partial modification | 200/204 |
| DELETE | /api/notices/42 | Remove resource association | 204 |

Safe methods are intended for read-only semantics. Idempotence means repeated identical requests have the same intended server effect as one request, not necessarily identical responses. GET, PUT and DELETE are idempotent by HTTP semantics; POST and PATCH are not guaranteed to be. Repeated DELETE can return 204 then 404 without contradicting idempotence. Application-specific idempotency keys and transaction constraints can reduce duplicate POST effects.

Illustrative GET response:
```json
{"id":42,"content":"Class begins at 09:00"}
```

## 6. Original Flask API example

```python
from flask import Flask, jsonify, request

app = Flask(__name__)
notices = [{"id": 42, "group": "1A", "content": "Class notice"}]

@app.get("/api/notices")
def list_notices():
    group = request.args.get("group")
    if group is not None and group not in {"1A", "1B"}:
        return jsonify(error="Invalid group"), 400
    return jsonify([n for n in notices if group is None or n["group"] == group])

@app.get("/api/notices/<int:notice_id>")
def get_notice(notice_id):
    notice = next((n for n in notices if n["id"] == notice_id), None)
    return (jsonify(notice), 200) if notice else (jsonify(error="Not found"), 404)
```
@app.get maps a GET route; request.args reads query parameters; int: converts the path variable; jsonify creates JSON responses; next retrieves the first matching item with a fallback. This fictional public-notice demo is intentionally not a production protected-data API. Real student records require authenticated and authorized scoped queries. In-memory data is not persistent.

## 7. Security and project relevance

- BOLA/IDOR: an identifier is not authorization. Check caller ownership, role and tenant on every object access.
- Broken function-level authorization: protect teacher/admin actions on the server.
- Injection: parameterize SQL and validate query types and allowed fields.
- Mass assignment: allowlist writable fields; never let a client set arbitrary role or ownership.
- CSRF: protect state-changing cookie-authenticated operations. CORS is not authentication.
- XSS: encode output for its rendering context; validate unsafe HTML.
- Rate limits and pagination: constrain resource usage; avoid exposing private properties.
- SOAP/XML: disable unsafe external entity/DTD processing and apply parser limits.
- TLS and secrets: use HTTPS, validate certificates, protect tokens, do not expose service credentials.
- Dependencies: use supported framework releases, lockfiles, updates and tests.
- UniTrack: Flask routes must enforce session, role and object checks for QR, visitor and access records.
- ProTrack-MX: React calls Supabase APIs backed by PostgreSQL; RLS policies enforce access to permitted groups/records. UI checks alone are insufficient. Never publish service-role keys.

## 8. Mandatory lab — pending

The supplied question asks to use a GET request with the id parameter in the assigned HTB index.php endpoint and identify the name associated with user ID 1. Spawn the assigned target. Observe the provided starting request and change only the id value to the requested identifier. Inspect the returned content and identify the actual name field. A generic local example is curl -i 'http://127.0.0.1:8080/index.php?id=1' (illustrative only, not the assigned target). -i displays headers and body; quoting protects shell metacharacters. Use the assigned URL with its actual scheme and port privately. If HTML is returned, inspect the displayed page or source; if JSON is returned, inspect the actual field before filtering. No target address, output, answer, or acceptance has been supplied; do not guess or publish restricted answers. Update this note with sanitized methodology once evidence is supplied.

## References

- [HTB Academy](https://academy.hackthebox.com/app/module/75)
- [RFC 9110 HTTP Semantics](https://www.rfc-editor.org/rfc/rfc9110.html)
- [W3C SOAP 1.2](https://www.w3.org/TR/soap12-part1/)
- [Fielding REST dissertation](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)
- [OWASP API Security](https://owasp.org/www-project-api-security/)
- [Framework guides](Development-Frameworks/README.md)

Examples are independent and illustrative. No local installation, project modification or live testing claimed.
