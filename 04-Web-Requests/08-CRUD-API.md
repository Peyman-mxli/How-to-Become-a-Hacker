# Lesson 08/08 — CRUD API

## Learning objectives
Understand resource-oriented web APIs, CRUD operations, JSON responses, cURL request construction, state verification, and API authorization.

## APIs and resources
An application programming interface defines how software components communicate. A web API can expose resources through HTTP endpoints, validate requests, execute business logic, and return representations such as JSON. The supplied lab exposes a city resource through /api.php/city/. Its path suffix selects or searches for a city. A resource does not necessarily correspond directly to a database table, and a path selector is not universally an exact identifier.

## CRUD and HTTP methods

| Operation | Common HTTP method | Conceptual SQL equivalent |
| --- | --- | --- |
| Create | POST | INSERT |
| Read | GET | SELECT |
| Update | PUT or PATCH | UPDATE |
| Delete | DELETE | DELETE |

These are conventional mappings, not requirements for every API. POST can perform operations other than creation; PUT may create or replace a resource at a known URI; PATCH applies partial changes; DELETE removes the association with the target resource, not necessarily physical database rows. The application defines its supported methods and resource semantics.

## Read and JSON formatting
Independent examples below assume an owned, disposable local API that implements the illustrated routes. They are not executed commands or actual UniTrack routes.

~~~bash
curl -sS http://127.0.0.1:8080/api/cities/Paris
curl -sS http://127.0.0.1:8080/api/cities/Paris | jq .
~~~

Illustrative response:
~~~json
[
  {
    "city_name": "Paris",
    "country_name": "France"
  }
]
~~~

GET is selected by default. -s suppresses the progress meter and error messages; -S restores error messages when used with -s. The pipe passes response bytes to jq, a JSON processor. The dot is the identity filter, which formats the JSON without selecting a particular field. jq is optional; raw JSON is readable. A Python 3 alternative is python3 -m json.tool.

Additional example:
~~~bash
curl -sS http://127.0.0.1:8080/api/cities/Paris | jq -r '.[0].city_name'
~~~

.[0] selects the first element of an array; .city_name selects the field; -r emits a string without JSON quotation marks. Illustrative output: Paris. Check the array is nonempty before assuming an element exists. The supplied lab supports substring search and a collection route; other APIs may implement pagination, exact identifiers or query parameters instead.

## Create
~~~bash
curl -i -H 'Content-Type: application/json' -d '{"city_name":"DemoCity","country_name":"DemoCountry"}' http://127.0.0.1:8080/api/cities/
~~~

-d supplies the body and normally selects POST. -H specifies the JSON media type. -i includes response headers. A creation response might be 201 Created, but the API contract determines the status. Verify the new disposable record with GET.

## Update
~~~bash
curl -i -X PUT -H 'Content-Type: application/json' -d '{"city_name":"DemoCityUpdated","country_name":"DemoCountry"}' http://127.0.0.1:8080/api/cities/DemoCity
~~~

-X explicitly selects PUT. The path identifies the original resource and the body specifies its replacement representation. Depending on the API, PUT might create a missing resource or reject the request. Verify both the old and new resource names if the API uses names as identifiers. Some APIs use stable IDs, so renaming does not change the URI.

PATCH illustration, only if supported by the API:
~~~bash
curl -i -X PATCH -H 'Content-Type: application/json' -d '{"country_name":"UpdatedCountry"}' http://127.0.0.1:8080/api/cities/DemoCityUpdated
~~~

The actual patch media type and format depend on the API. OPTIONS can advertise supported methods but does not establish that the current user is authorized to use them.

## Delete
~~~bash
curl -i -X DELETE http://127.0.0.1:8080/api/cities/DemoCityUpdated
curl -sS http://127.0.0.1:8080/api/cities/DemoCityUpdated
~~~

Use DELETE only on disposable test resources within an authorized environment. A successful deletion might return 204 No Content; a later GET might return an empty array or 404, depending on the contract. An empty response body alone is insufficient evidence that a mutation succeeded.

## Additional defensive API testing example
In an owned school management API, a teacher may read records for assigned classes but must not be able to edit unrelated records or delete users merely because a valid resource identifier is known. Enforce server-side object-level and function-level authorization for every HTTP method. Check both the acting user's role and the target object's relationship to that user. Use safe test fixtures rather than real student data. Record expected versus observed status and state, validate JSON schemas, use parameterized SQL, prevent mass assignment, enforce session/CSRF protections where applicable, and log authorized changes without exposing secrets. A successful request alone is not proof of an authorization vulnerability; permissions and intended behavior must be established. No actual project tests were performed.

## Mandatory HTB exercise
The assigned lab asks the learner to perform a city update, a deletion, and a final read in that order. All mutations must remain within the spawned training instance. Planned procedure:

1. Spawn the assigned target and read the city collection or selected entries.
2. Identify existing test records and choose two distinct records so the renamed resource is not deleted.
3. Perform the required PUT with a JSON representation, preserving the relevant fields unless the exercise requires otherwise. Verify the result as appropriate.
4. Perform the required DELETE on the other authorized lab record.
5. Read the renamed resource and inspect the actual response for the requested flag.
6. Extract the exact response field or line containing the flag, submit privately, and document sanitized observed evidence.

### Observed practical evidence

The learner used Ubuntu WSL2 and cURL against the assigned training instance. Initial GET requests returned existing London and Birmingham records, each with country_name set to (UK). Two distinct records were selected so the subsequent DELETE would not remove the renamed record.

Sanitized command templates:

~~~bash
curl -s http://TARGET_IP:PORT/api.php/city/ORIGINAL_CITY
curl -s http://TARGET_IP:PORT/api.php/city/OTHER_CITY
curl -i -X PUT http://TARGET_IP:PORT/api.php/city/ORIGINAL_CITY -d '{"city_name":"REQUESTED_NAME","country_name":"(UK)"}' -H 'Content-Type: application/json'
curl -i -X DELETE http://TARGET_IP:PORT/api.php/city/OTHER_CITY
curl -s http://TARGET_IP:PORT/api.php/city/REQUESTED_NAME
~~~

Both mutation requests returned HTTP/1.1 200 OK with Content-Length: 0. The final GET returned the renamed record, confirming the renamed resource was accessible and the requested flag was retrieved. Deletion was accepted according to the response status, but absence of the deleted record was not independently rechecked.

Sanitized final response, reformatted for readability:

~~~json
[
  {
    "city_name": "[REQUESTED_NAME]",
    "country_name": "[FLAG REDACTED]"
  }
]
~~~

Exact answer location: first object of the returned JSON array, in the country_name field. Extract only the flag string including its braces. A generic jq expression for extracting that field is jq -r '.[0].country_name'; the learner retrieved the answer directly from the raw response, not by running jq.

The assigned target, assessment-specific values and exact flag are omitted from public documentation. The learner subsequently supplied an HTB share preview stating Completed Web Requests, confirming overall module completion. Individual answer acceptance screens were not separately supplied.

## Verification
Section 8 theory studied and documented. All eight Web Requests lessons now have English notes. Mandatory CRUD requests and final flag retrieval verified from learner terminal output. PUT and DELETE returned 200 OK; final GET returned the renamed resource and flag. Independent post-deletion read was not performed. Official module completion confirmed by the learner's HTB completion share preview. Local examples are illustrative. Optional exercises skipped.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [HTTP PATCH, RFC 5789](https://www.rfc-editor.org/rfc/rfc5789)
- [JSON, RFC 8259](https://www.rfc-editor.org/rfc/rfc8259)
- [cURL manual](https://curl.se/docs/manpage.html)
- [jq manual](https://jqlang.org/manual/)
- [OWASP API Security Project](https://owasp.org/www-project-api-security/)

Independent English study notes based on the supplied HTB Academy Section 8, with original examples and technical clarifications.
