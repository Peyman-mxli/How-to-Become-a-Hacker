# cURL — HTTP Command-Line Client

## Definition and architecture

cURL is a free open-source command-line application using libcurl to transfer data over supported protocols. For HTTP, it parses the URL, connects to the destination, negotiates TLS for HTTPS, sends the request and receives response headers/body. It does not render webpages or execute browser JavaScript. libcurl is the underlying library, not the same interface as the CLI.

## Official downloads and documentation

- https://curl.se/download.html
- https://curl.se/windows/
- https://github.com/curl/curl
- https://github.com/curl/curl/releases
- https://curl.se/docs/manpage.html
- https://everything.curl.dev/

On Windows 11, first run curl.exe --version in PowerShell. If missing, use the official Windows downloads, extract the matching architecture build and add its executable directory to PATH if needed; reopen PowerShell and check again. Do not replace the Windows system executable. In older Windows PowerShell, curl can be an alias; curl.exe explicitly invokes the executable.

On Ubuntu WSL2:
~~~bash
curl --version
sudo apt update
sudo apt install curl
~~~
Install only if missing. sudo runs an authorized administrative command, apt update refreshes package information, apt install installs the named package. Windows and WSL environments have separate installations.

## Commands and configuration

| Option | Meaning |
| --- | --- |
| --version | Show version, protocols and features |
| -s | Suppress progress and errors |
| -S | Restore errors when silent |
| -X METHOD | Explicitly select request method |
| -d DATA | Send request data, normally selects POST |
| --data-urlencode | Encode form data |
| -H HEADER | Specify a header |
| -i | Include response headers |
| -v | Verbose transfer diagnostics |
| -o FILE | Save response body |
| -w FORMAT | Print transfer information |
| --connect-timeout SECONDS | Limit connection time |
| --max-time SECONDS | Limit total transfer time |
| -f | Return an error for HTTP error statuses |

Use the full manual to understand options. -s does not hide response body. Do not disable certificate verification as a routine workaround. Verbose output can contain sensitive headers; redact before sharing.

## Examples (owned local endpoints only)

GET:
~~~bash
curl -sS http://127.0.0.1:5000/api/status
~~~
Empty POST:
~~~bash
curl -sS -X POST http://127.0.0.1:5000/api/demo
~~~
Form data:
~~~bash
curl -sS -d "name=Student" -d "course=JavaScript" http://127.0.0.1:5000/api/demo
~~~
The body is name=Student&course=JavaScript. -d ordinarily selects POST automatically. Encode special characters with --data-urlencode when appropriate:
~~~bash
curl -sS --data-urlencode "name=Demo Student" http://127.0.0.1:5000/api/demo
~~~
JSON:
~~~bash
curl -sS -X POST -H "Content-Type: application/json" -d '{"name":"Student"}' http://127.0.0.1:5000/api/demo
~~~
This syntax uses Bash quoting; PowerShell native argument quoting can differ by version. Use Ubuntu WSL2 for consistency with HTB. Set the media type required by the server. Empty POST without -d differs from explicit empty -d in potential generated headers.

Inspect headers and status:
~~~bash
curl -sS -i http://127.0.0.1:5000/api/status
~~~
Illustrative output, not observed:
~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{"status":"ok"}
~~~
Save a response without executing it:
~~~bash
curl -sS -o response.json http://127.0.0.1:5000/api/status
~~~

## Original local demo server

Optional reference only, not an HTB requirement. In a disposable folder, save demo.py:
~~~python
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.get("/api/status")
def status():
    return jsonify(status="ok")

@app.post("/api/demo")
def demo():
    if request.is_json:
        return jsonify(received=request.get_json())
    return jsonify(received=request.form.to_dict())

if __name__ == "__main__":
    app.run(host="127.0.0.1", port=5000)
~~~
Use a separate Python virtual environment and install Flask from https://pypi.org/project/Flask/ if needed:
~~~bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install flask
python demo.py
~~~
On Windows use py -m venv .venv and .\.venv\Scripts\Activate.ps1, then python -m pip install flask and python demo.py. Use another terminal to run the examples. Expected response to the JSON example: {"received":{"name":"Student"}}; the empty POST returns {"received":{}}. Formatting may differ. Bind to loopback only, stop with Ctrl+C, and do not use Flask development server in production. No installation or execution verified.

## Troubleshooting and security

| Symptom | Next check |
| --- | --- |
| Command not recognized | PATH or installation; use curl.exe on Windows |
| Could not resolve host | Check URL and DNS |
| Connection refused/timeout | Check local server or authorized lab connectivity and timer |
| HTTP 404 | Check route and URL |
| HTTP 405 | Check request method |
| HTTP 401/403 | Check legitimate access requirements |
| HTTP 415 | Check Content-Type and body |
| HTTP 500 | Review owned server logs |
| No visible body | Check status/headers; some responses are legitimately empty |
| TLS error | Check hostname, trust chain and system clock |

Only test owned systems or expressly assigned labs. Avoid unnecessary state-changing requests, protect cookies and credentials, redact logs and keep HTB assessment values out of public notes. For UniTrack, enforce Flask authorization and validate QR and visitor operations. For ProTrack-MX, protect student data and Supabase privileged keys with RLS and backend authorization. cURL is a client, not an authorization mechanism.

Related: [Browser source inspection](01-Browser-Source-Inspection.md), [XHR](14-XMLHttpRequest.md), [HTTP Requests lesson](../08-HTTP-Requests.md). Supplement only; no additional HTB credit.
