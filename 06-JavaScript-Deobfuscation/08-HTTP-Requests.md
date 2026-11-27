# Section 08 — HTTP Requests

**Module:** JavaScript Deobfuscation | **Section:** 8/11

**Status:** Theory documented from learner-supplied HTB lesson. One mandatory question; actual POST response pending learner terminal evidence. Displayed rewards +15 section and +30 question are not verified as earned. No new installation or live request performed by the assistant.

## 1. Objective and previous analysis

The recovered JavaScript from Sections 6–7 describes an asynchronous POST request to /serial.php, without an explicit request body:

~~~javascript
function generateSerial() {
    var xhr = new XMLHttpRequest;
    var url = "/serial.php";
    xhr.open("POST", url, true);
    xhr.send(null);
}
~~~

The objective is to reproduce its method and resource path using cURL and examine the actual server response. A browser XHR request and a command-line request may differ in cookies, headers and other client behavior. The supplied lesson's final paragraph refers to server.js; the preceding sections and current question identify secret.js as the analyzed resource. The actual backend behavior has not been verified. No assessment flag or live target address is published.

## 2. cURL and libcurl

cURL is a free open-source command-line data transfer client. libcurl is the transfer library used by the CLI and other applications. HTTP is a web communication protocol; HTTPS is HTTP protected by TLS. Common uses include retrieving pages, testing owned APIs, examining headers, submitting forms, downloading files and troubleshooting.

Official access:
- https://curl.se/
- https://curl.se/download.html
- https://curl.se/windows/
- https://github.com/curl/curl
- https://curl.se/docs/manpage.html
- https://everything.curl.dev/

Windows PowerShell:
~~~powershell
curl.exe --version
~~~
Use curl.exe explicitly to avoid possible Windows PowerShell aliases. Ubuntu WSL2:
~~~bash
curl --version
sudo apt update
sudo apt install curl
~~~
Only install when missing. sudo invokes authorized administrative privileges; apt is the package manager; update refreshes package metadata; install curl installs the package; --version reports release and features. Learner already demonstrated cURL use; no reinstall necessary.

## 3. GET request

~~~bash
curl http://SERVER_IP:PORT/
~~~
A URL-only HTTP request ordinarily defaults to GET. An illustrative owned local server:
~~~bash
curl http://127.0.0.1:5000/
~~~
The response may contain HTML, JSON, plain text or other data. cURL does not render HTML or execute JavaScript. Illustrative HTML:
~~~html
<!DOCTYPE html>
<html>
<head><title>Example Application</title></head>
<body><h1>Welcome</h1></body>
</html>
~~~

## 4. POST request

HTB demonstrates:
~~~bash
curl -s http://SERVER_IP:PORT/ -X POST
~~~
Equivalent option ordering with error reporting:
~~~bash
curl -sS -X POST http://SERVER_IP:PORT/
~~~
-X explicitly selects the HTTP method; POST is the method; -s hides progress and routine errors; -S restores errors with -s. Silent mode does not hide the response body. The URL contains scheme, host, port and resource path. Use the assigned lab only for mandatory exercises.

## 5. POST data

~~~bash
curl -s -X POST -d "param1=sample" http://SERVER_IP:PORT/
~~~
-d sets the request body and normally selects POST automatically, with application/x-www-form-urlencoded as default content type.
~~~bash
curl -sS -d "param1=sample" http://SERVER_IP:PORT/
~~~
The body is param1=sample. Multiple -d options are combined with ampersands. Original illustrative owned local example:
~~~bash
curl -sS -X POST \
  -d "name=Student" \
  -d "course=JavaScript" \
  http://127.0.0.1:5000/api/demo
~~~
Body: name=Student&course=JavaScript. Backslashes continue the Bash command on the next line; PowerShell uses different continuation syntax. The example endpoint must exist. For values containing spaces or special characters, --data-urlencode can encode form fields.

## 6. Request comparison

| Option | Meaning |
| --- | --- |
| -X POST | Explicit POST, no body supplied by this option |
| -d "param1=sample" | Submit form-style body, normally selects POST |
| -d "" | Explicit zero-length body, normally selects POST |
| -i | Include response headers |
| -v | Verbose connection/request/response diagnostics |
| -o filename | Save response body to file |
| -w | Print selected transfer information after transfer |
| -H | Set request header |

Use -X POST alone for the supplied empty-body behavior; -d is not necessary. An explicit empty body may cause different automatically generated headers. POST is not inherently secure; cookies and headers may still carry information. GET is intended for retrieval; POST may cause state changes. Do not repeat state-changing requests without a reason.

Original supplemental examples for an owned demo service, not executed:
~~~bash
curl -sS -i http://127.0.0.1:5000/api/status
curl -sS -X POST -d "name=Student" http://127.0.0.1:5000/api/demo
curl -sS -X POST -H "Content-Type: application/json" \
  -d '{"name":"Student"}' http://127.0.0.1:5000/api/demo
~~~
The first includes HTTP status and response headers; the second uses form data; the third uses a JSON body and declares the media type. These are different request representations and require corresponding local routes.

## 7. Mandatory HTB exercise

Question: send POST to /serial.php and report the actual response. The recovered function calls send(null), so do not add arbitrary data. Run the following against the explicitly assigned lab, substituting the active host and port:
~~~bash
curl -sS -X POST http://SERVER_IP:PORT/serial.php
~~~
Use the actual response body as evidence; do not assume a flag or decode the response until the next task requires it. If an error occurs, inspect it and check the timer, exact target and connectivity. A POST request can have side effects; avoid unnecessary repeats. The response is not yet supplied, so the exact answer remains pending. No live request is claimed by the assistant. Record sanitized evidence after the learner provides the output; do not publish the lab host or assessment answer.

## 8. Project applications and defensive interpretation

UniTrack: use cURL to test explicitly authorized development APIs, including legitimate QR workflows, visitor passes and authorization checks. Flask must independently verify roles, QR validity, session and expiration. ProTrack-MX: test authorized application APIs without exposing student records or privileged Supabase keys; enforce RLS and backend authorization. Use HTTPS, input validation, CSRF controls where applicable and avoid logging secrets. Discovering a route or receiving HTTP 200 is not automatically proof of a vulnerability. These are recommendations, not new audit findings.

## 9. References

- [HTB JavaScript Deobfuscation](https://academy.hackthebox.com/course/preview/javascript-deobfuscation)
- [cURL manual](https://curl.se/docs/manpage.html)
- [Everything curl](https://everything.curl.dev/)
- [Existing browser source inspection guide](Tools/01-Browser-Source-Inspection.md)
- [Dedicated cURL guide](Tools/15-cURL.md)
- [XMLHttpRequest guide](Tools/14-XMLHttpRequest.md)

Learner advanced to Section 8; separate Section 7 platform completion not supplied. Next action: obtain the actual POST response and answer the mandatory question. No additional section completion or earned points verified.
