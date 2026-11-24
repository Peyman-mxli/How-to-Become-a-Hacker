# Lesson 01/08 — HyperText Transfer Protocol (HTTP)

## Learning objectives
Understand HTTP client-server communication, URLs, name resolution, the basic request/response lifecycle, and cURL as a command-line HTTP client.

## HTTP fundamentals
HTTP is an application-layer request/response protocol. A client (browser, mobile application, cURL or API client) asks a server to perform an operation on a resource. The server processes the request and returns a response containing a status code, headers and, when applicable, a body. Resources can include HTML, images, JSON or downloadable files. Hypertext links connect resources.

HTTP defaults to TCP port 80; HTTPS defaults to port 443 and protects HTTP communications with TLS (HTTP/3 uses QUIC over UDP). Alternate ports such as 8080 and 8443 are configurable conventions, not guarantees of protocol or encryption. HTTP without TLS does not protect data against interception or tampering in transit.

## URL anatomy
Illustrative URL (dummy credentials for explanation only):
`http://demo:example@example.com:80/dashboard.php?view=summary&lang=en#status`

| Part | Example | Purpose |
| --- | --- | --- |
| Scheme | http:// | Selects protocol |
| User info | demo:example@ | Optional user information; deprecated for HTTP(S) URLs and often rejected |
| Host | example.com | Domain name or IP address |
| Port | :80 | Destination service port; defaults depend on scheme |
| Path | /dashboard.php | Identifies a resource or application route |
| Query | ?view=summary&lang=en | Input parameters; & separates pairs |
| Fragment | #status | Client-side reference, normally not included in the HTTP request |

An absolute HTTP URL needs a scheme and host. A missing path is represented as / in an HTTP request; it does not guarantee an index file, since the server may route, redirect or reject it. A fully qualified domain name identifies a domain in the DNS hierarchy; it is not itself a complete URL. Avoid credentials and sensitive tokens in URLs because history, logs and referrers may expose them. Treat query parameters as untrusted input, not proof of authentication.

## Request lifecycle
1. The client parses the URL and determines the host, port and path.
2. If necessary, the operating system or browser resolves the name, using caches, host mappings and DNS according to its configuration. Linux commonly consults /etc/hosts through name-service configuration; Windows uses C:\Windows\System32\drivers\etc\hosts. DNS may also be handled by a proxy or browser-specific resolver. An IP literal does not need DNS resolution.
3. The client establishes or reuses a connection; HTTPS also requires a secure transport handshake where applicable.
4. The client requests the resource. A typical browser navigation uses GET.
5. The server processes the request, checks permissions as appropriate, and returns a response.
6. A browser interprets the response and may fetch related resources such as CSS, JavaScript and images. cURL normally prints the response body instead.

Illustrative HTTP/1.1 request:
~~~http
GET /products?category=books HTTP/1.1
Host: example.com
User-Agent: ExampleClient/1.0

~~~

Illustrative response:
~~~http
HTTP/1.1 200 OK
Content-Type: application/json

{"category":"books","count":2}
~~~

GET requests a representation of a resource. Host identifies the intended virtual host. User-Agent describes the client. Status 200 means the request succeeded, while Content-Type describes the representation. These are invented examples, not captured traffic. A 200 response does not establish that authentication or authorization is correctly implemented.

## cURL
cURL is a command-line transfer tool backed by libcurl. It supports HTTP and many other protocols, making it useful for reproducible debugging, API testing and authorized security assessments. A browser renders web pages; cURL does not execute JavaScript or render HTML.

The lesson references CERN's historic first website. A normal public GET request is a simple demonstration, not an authorization to test the site's security:
~~~bash
curl http://info.cern.ch/
~~~

Download a named resource:
~~~bash
curl -O http://info.cern.ch/index.html
curl -o first-page.html http://info.cern.ch/index.html
curl -s -O http://info.cern.ch/index.html
ls -l index.html
~~~

These commands are instructional, not executed for this lesson. -O uses the last remote URL path component as the filename. -o chooses an explicit filename. Both may overwrite existing local files; work in a dedicated folder. -s suppresses the progress meter and error messages, not a response body sent to stdout. Combine -s with -S to retain errors.

| Option | Meaning |
| --- | --- |
| -O, --remote-name | Save using URL-derived filename |
| -o, --output FILE | Write response body to named file |
| -s, --silent | Suppress progress and errors |
| -S, --show-error | Show errors when silent mode is enabled |
| -h, --help | Show help; optionally specify a category |
| -i, --include | Include response headers with body |
| -I, --head | Request HTTP HEAD |
| -v, --verbose | Show connection and protocol details, generally on stderr |
| -d, --data DATA | Send data; normally selects POST unless another method is requested |
| -u, --user USER | Specify authentication user; enter password at prompt if needed |
| -A, --user-agent NAME | Set the User-Agent header |
| -L, --location | Follow redirects; verify destination remains authorized |
| -f, --fail | Return an error for HTTP failures such as 404, instead of saving a normal error body |

Options are case sensitive. -O and -o have different behavior. -i and -I are not equivalent. Verbose output may reveal sensitive headers; sanitize before sharing.

~~~bash
curl --version
curl -h
curl --help all
curl -h http
man curl
~~~

--version identifies the installed version, supported protocols and features. --help all lists options; -h http selects HTTP help; man curl opens the local manual if installed.

## Environment and installation
The learner already uses Ubuntu WSL2, Windows PowerShell and HTB Pwnbox. Check curl --version in Ubuntu first. Only if missing:
~~~bash
sudo apt update
sudo apt install curl
~~~
apt update refreshes package indexes; apt install curl installs the package; sudo requests authorized elevation. Pwnbox normally has cURL. In Windows PowerShell use curl.exe to explicitly select the native executable (curl may be an alias in older Windows PowerShell versions). Check curl.exe --version. No new installation was performed or required in this lesson.

## Additional original example: local API testing
Assume a learner-owned Flask development API is already running on loopback and exposes a hypothetical /api/health route. This is an illustration, not a claim about an existing project endpoint.
~~~bash
curl -i http://127.0.0.1:5000/api/health
curl -sS -o health.json http://127.0.0.1:5000/api/health
cat health.json
~~~
Possible illustrative response: {"status":"ok"}. Inspect the HTTP status and content type before trusting the body. -sS combines silent mode and show-error. cat reads the saved file. No local server or commands were executed for this example.

Another example: download a known text resource from a learner-owned server:
~~~bash
curl -fS -o report.txt http://127.0.0.1:8080/report.txt
file report.txt
~~~
file identifies the apparent file type. Do not execute untrusted downloads. The endpoint must exist before this example can work.

## Security and real-world use
- Developers reproduce API failures and inspect response status and headers.
- Authorized testers examine client-server behavior, input handling and access-control enforcement within scope.
- Defenders investigate unusual requests, validate TLS and avoid logging secrets.
- A VPN does not replace HTTPS. HTTP can remain unencrypted beyond the VPN tunnel.
- Use only assigned HTB targets or owned/explicitly authorized systems. Do not publish live lab addresses, flags, credentials or restricted assessment solutions.

## Required HTB exercise
The supplied section requires retrieving a file from an assigned lab endpoint and submitting its flag. The learner spawned the assigned target and used Ubuntu WSL2 to retrieve the file. Terminal output showed 20 bytes received. The learner then used cat to read the file and obtained the flag. The live target and flag are intentionally omitted.

## Verification and progress
- Section 1 of 8: theory studied and independently documented.
- Mandatory exercise: download and flag extraction verified from learner terminal output. The learner advanced to Section 2; individual HTB answer acceptance was not separately shown.
- No commands executed against a target by the assistant. Examples are illustrative.
- Platform completion: learner advanced to Section 2; no separate completion screenshot supplied.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [cURL official downloads](https://curl.se/download.html)
- [cURL manual](https://curl.se/docs/manpage.html)
- [HTTP semantics (RFC 9110)](https://www.rfc-editor.org/rfc/rfc9110)
- [URI syntax (RFC 3986)](https://www.rfc-editor.org/rfc/rfc3986)
- [CERN: birth of the web](https://home.cern/science/computing/birth-web)

Independent study notes based on the supplied HTB Academy lesson, with original examples and technical clarifications.
