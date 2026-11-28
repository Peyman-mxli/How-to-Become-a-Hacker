# Burp Suite Community Edition

PortSwigger's free (not open-source) desktop web testing toolkit provides Proxy, HTTP history, Repeater, Decoder, Comparer and scope organization. Proxy listener mediates HTTP(S); HTTPS inspection terminates TLS in a trusted test browser and establishes separate upstream TLS. Community lacks some paid capabilities.

```mermaid
flowchart LR
    B[Dedicated browser] --> P[Loopback Burp Proxy]
    P --> A[Owned local application]
```
[Download](https://portswigger.net/burp/communitydownload), [install](https://portswigger.net/burp/documentation/desktop/getting-started/download-and-install), [CA trust](https://portswigger.net/burp/documentation/desktop/external-browser-config/certificate). Windows: official installer, verify publisher, launch temporary project and defaults, open built-in browser via Proxy. Linux: choose official installer for supported architecture and desktop. WSL graphical support and network namespace vary. Bind listener to loopback, commonly 127.0.0.1:8080; avoid exposing to LAN. Built-in browser is preconfigured. External test browser may require dedicated profile and CA trust. Remove temporary trust when done; do not disable TLS verification.

Original local example: with owned Flask development server running at 127.0.0.1:5000, navigate to root in built-in browser. Proxy history displays GET /, Host and response. Intercept may pause requests; Forward releases selected request, Drop cancels it. Repeater allows authorized manual inspection of a benign request. Illustrative response 200 OK is not observed. Optional reference:
```bash
curl --noproxy "" --proxy http://127.0.0.1:8080 -i http://127.0.0.1:5000/
```
--noproxy clears bypass for this call; --proxy selects listener; -i includes headers. Windows use curl.exe if alias conflicts. If waiting check Intercept; if refused check listener, address/port, WSL separation and local server. Avoid actual credentials and student data; redact cookies/tokens. UniTrack and ProTrack-MX testing only against authorized development/staging accounts. No installation or execution performed.
