# XMLHttpRequest (XHR) — Browser HTTP API

## Definition and architecture

XMLHttpRequest is a browser-provided interface for programmatic HTTP requests and responses. AJAX (Asynchronous JavaScript and XML) describes a style of updating pages without full reloads, often using JSON today. A typical flow: browser creates request object, configures method and URL, registers event handlers, sends request, receives response and handles result. XHR uses event callbacks; Fetch typically uses promises. XHR is not limited to XML and is not a server framework.

## Official access and setup

- [WHATWG standard](https://xhr.spec.whatwg.org/)
- [MDN reference](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- [MDN usage guide](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest_API/Using_XMLHttpRequest)
- [Chrome official download](https://www.google.com/chrome/)
- [Firefox official download](https://www.mozilla.org/firefox/new/)

No standalone XHR download is necessary. Install Chrome or Firefox through its official installer if missing. On Windows 11, open the downloaded installer; in WSL2 use the Windows browser for local examples. VS Code and Python are already in the learner environment. Node does not normally include native XHR; use a browser, or Fetch for a modern Node runtime. F12 opens browser DevTools; Network shows the request and response.

## Methods, properties and configuration

| Member | Purpose |
| --- | --- |
| new XMLHttpRequest() | Create request object |
| open(method, url, async) | Configure the request; true means asynchronous |
| setRequestHeader(name, value) | Set allowed request header after open and before send |
| send(body) | Initiate request, null means no explicit body |
| abort() | Cancel request |
| timeout | Set timeout in milliseconds |
| responseType | Choose response representation, e.g. text or json |
| status | HTTP response code |
| responseText | Text response when responseType is compatible |
| response | Response in configured representation |
| onload | Transfer completed; check HTTP status separately |
| onerror | Network-level failure |
| ontimeout | Timeout |
| readyState | Request lifecycle state |

| readyState | Meaning |
| --- | --- |
| 0 UNSENT | Object created |
| 1 OPENED | open called |
| 2 HEADERS_RECEIVED | Status and response headers available |
| 3 LOADING | Receiving response body |
| 4 DONE | Finished, possibly failed |

## Original local example (reference only)

Create a disposable folder xhr-demo, and in VS Code create status.json containing:
~~~json
{"status":"ok"}
~~~
Create index.html in the same folder:
~~~html
<!doctype html>
<html lang="en">
<head><meta charset="utf-8"><title>XHR Demo</title></head>
<body>
  <button id="check">Check status</button>
  <pre id="output"></pre>
  <script>
    document.getElementById("check").addEventListener("click", () => {
      const output = document.getElementById("output");
      const xhr = new XMLHttpRequest();
      xhr.open("GET", "/status.json", true);
      xhr.timeout = 5000;
      xhr.onload = () => {
        if (xhr.status >= 200 && xhr.status < 300) {
          output.textContent = xhr.responseText;
        } else {
          output.textContent = "HTTP error: " + xhr.status;
        }
      };
      xhr.onerror = () => { output.textContent = "Network error"; };
      xhr.ontimeout = () => { output.textContent = "Request timed out"; };
      xhr.send(null);
    });
  </script>
</body>
</html>
~~~
Start the local Python development server from that folder (Windows PowerShell):
~~~powershell
python -m http.server 8000 --bind 127.0.0.1
~~~
In Ubuntu WSL2 use python3 instead of python if appropriate. -m runs a Python module; http.server serves the current directory; 8000 is the port; --bind 127.0.0.1 restricts the server to loopback. Open http://127.0.0.1:8000/ in the browser; click Check status. Expected display: {"status":"ok"}. Open F12 > Network to inspect the GET request and status. Stop the server with Ctrl+C. This reference example is not an additional required HTB lab, and no execution was verified. Use only a folder containing demonstration files, not a sensitive directory. Python's basic HTTP server is not a production server.

The event listener waits for the click; open configures the request; timeout limits waiting to five seconds; onload checks status; textContent renders received text without interpreting it as HTML; send(null) initiates a GET without body. For a POST to an owned service, open with POST and use the body format required by that service; do not assume the server supports it.

## Troubleshooting

- XMLHttpRequest is not defined: use a browser context; Node has no native XHR by default.
- HTTP 404: check URL, file or route.
- HTTP 405: server rejects the chosen method.
- HTTP 401/403: examine legitimate authentication and authorization requirements.
- HTTP 500: inspect server logs on systems you own.
- status 0 or error event: inspect Network/Console for connectivity, CORS, timeout or aborted request.
- No response handler: register callbacks before send.
- CORS error: use correct server-side policy for explicitly permitted origins; do not disable browser protections.
- file:// issues: serve the example through the local HTTP server.
- InvalidStateError: check open/send order and responseType.

## Security and real-world application

Same-origin policy limits cross-origin browser access; CORS permits selected cross-origin response access by HTTP headers. Origin consists of scheme, host and port. CORS does not replace backend authentication, authorization or CSRF defenses. Cookies can accompany eligible requests even with no explicit body. Use HTTPS in production, validate authorization and inputs on the server, avoid secrets in browser source, apply CSRF controls where applicable and render untrusted text via textContent.

UniTrack can use AJAX-style requests for QR scanner status and notifications while enforcing Flask access rules. ProTrack-MX can use APIs and Supabase realtime for messaging and dashboard updates while protecting records with RLS and backend authorization. No new audit findings are claimed.

References: https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/readyState ; https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/send ; https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS ; https://docs.python.org/3/library/http.server.html

Supplemental guide only; no extra HTB section credit or actual tool execution claimed.
