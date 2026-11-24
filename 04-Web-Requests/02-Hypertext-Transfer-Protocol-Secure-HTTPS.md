# Lesson 02/08 — Hypertext Transfer Protocol Secure (HTTPS)

## Learning objectives
Understand the risks of clear-text HTTP, how TLS protects HTTP communications, certificate validation, HTTPS redirects, and cURL behavior with certificates.

## HTTPS and the security model
HTTPS is HTTP protected by TLS. Its ordinary default port is 443, whereas HTTP defaults to 80. HTTPS provides confidentiality (protecting data from unauthorized reading), integrity (detecting alteration), and server authentication (verifying the intended peer using a trusted certificate). It does not guarantee the application itself is secure or that the operator is trustworthy.

A network observer with access to unencrypted HTTP may read or modify requests and responses. For example, this invented HTTP/1.1 body contains dummy credentials:

~~~http
POST /login HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded

username=demo&password=example
~~~

With HTTPS, the HTTP method, headers, path, query and body are ordinarily protected in transit between the TLS endpoints. An observer may still see IP addresses, transport ports, packet sizes, timing and sometimes the server hostname. TLS termination at a reverse proxy means the connection from that proxy to an application backend must be secured separately where appropriate.

A man-in-the-middle (MITM) attack involves an unauthorized intermediary intercepting or modifying communications. Correct TLS certificate verification is essential for resisting active impersonation. Use packet capture only on owned or explicitly authorized networks. No interception was performed for this lesson.

## HTTPS flow and redirects
An initial HTTP request may receive a redirect such as:

~~~http
HTTP/1.1 301 Moved Permanently
Location: https://example.com/
~~~

301 indicates a permanent redirect; Location identifies the destination. Other redirect status codes are possible. The client then makes a separate connection to the HTTPS endpoint. A redirect alone does not protect the initial HTTP request; do not transmit secrets over that initial connection. Browsers may use HTTPS-first policies or HSTS to avoid clear-text navigation.

For a typical full TLS 1.3 handshake with server certificate authentication:
1. ClientHello advertises supported versions, algorithms and key shares, among other information.
2. ServerHello selects compatible parameters and supplies a key share.
3. Both peers derive shared handshake keys. The server sends encrypted handshake messages including its certificate and proof of possession of its private key.
4. The client validates the server identity, trust chain and certificate validity, and verifies the handshake proof.
5. Finished messages confirm handshake integrity; peers derive application traffic keys and exchange protected HTTP data.

This is a simplified model; resumed sessions, TLS 1.2 and QUIC-based HTTP/3 differ. TLS normally authenticates only the server. Client certificates are optional and used in mutual TLS when configured. Certificates authenticate public keys; private keys are never exchanged. SSL is obsolete terminology for modern deployments; use TLS 1.2 or 1.3 as appropriate.

## DNS and privacy clarification
Traditional DNS queries may disclose a hostname, not the full HTTPS URL path or query. Public resolver addresses 8.8.8.8 and 1.1.1.1 do not automatically enable encryption: configure DNS over HTTPS (DoH) or DNS over TLS (DoT) on a supported client. TLS Server Name Indication may also reveal the hostname unless protected by a mechanism such as ECH. IP addresses and traffic patterns remain visible. A VPN shifts trust to the VPN provider and does not guarantee end-to-end confidentiality.

## Certificates and validation
A server certificate binds a public key to one or more identities (typically DNS names in Subject Alternative Name). The client checks trust chain, intended hostname and validity period. Certificates may fail validation because of expiration, a mismatched name, a missing intermediate, an untrusted issuer, or local trust/time configuration. A self-signed certificate is not inherently weak encryption, but it is not automatically trusted.

## cURL for HTTPS
~~~bash
curl https://example.com/
~~~

cURL automatically negotiates TLS and checks the server certificate by default. Illustrative failure:
~~~text
curl: (60) SSL certificate problem: unable to get local issuer certificate
~~~

Error 60 indicates peer certificate verification failed; the exact message varies. Diagnose certificate configuration rather than disabling validation routinely.

~~~bash
curl -k https://localhost:8443/
~~~

-k / --insecure disables certificate verification (including hostname verification for HTTPS). Traffic can still be encrypted but the intended peer is not reliably authenticated, allowing active MITM impersonation. This is an illustrative option for an isolated, authorized lab with a known test certificate, never a production fix. The server must exist for this command to work.

A safer alternative for a trusted local development CA:
~~~bash
curl --cacert ./dev-ca.pem https://localhost:8443/
~~~

--cacert selects a trusted CA certificate file in PEM format. The server certificate must chain to that CA and match the hostname. Keep CA private keys private; only the public CA certificate is used here.

## Additional original examples
Inspect HTTPS headers and certificate negotiation on an owned test server:
~~~bash
curl -v -I https://localhost:8443/
~~~
-v shows diagnostic details including negotiation, depending on the TLS backend; -I sends HEAD. Some servers do not implement HEAD. Diagnostic output can include sensitive headers and should be sanitized.

Inspect a redirect without following it, then follow it only if the destination remains in scope:
~~~bash
curl -I http://localhost:8080/
curl -I -L --proto-redir =https http://localhost:8080/
~~~
-L follows redirects; --proto-redir =https allows only HTTPS redirect destinations. The first HTTP request is still unencrypted. These examples assume an existing local service; no service was started and no output was observed.

A Flask application such as the learner's UniTrack can use HTTPS at a production reverse proxy and appropriate secure session-cookie settings. Do not treat an HTTPS connection as a substitute for authorization, CSRF protection or secure password storage. A local development HTTP server bound to loopback is not a public production deployment.

## Defensive mitigations
Use HTTPS from the start, valid certificates, modern TLS settings, and HTTP Strict Transport Security (HSTS) once the HTTPS deployment is ready. HSTS is learned over a valid HTTPS connection (or from a preload list where supported); it helps prevent subsequent HTTP navigation and downgrade risks. Secure cookies should not be transmitted on ordinary HTTP connections. Avoid bypassing certificate warnings. HTTPS protects transport, not compromised endpoints or vulnerable application logic.

## Verification and progress
Section 2 theory studied and independently documented. The supplied lesson contains no mandatory question. All commands and outputs in this file are illustrative; no new packet capture, certificate bypass, or server test was performed. Platform completion has not been separately confirmed.

## References
- [HTB Academy](https://academy.hackthebox.com/)
- [HTTP semantics, RFC 9110](https://www.rfc-editor.org/rfc/rfc9110)
- [TLS 1.3, RFC 8446](https://www.rfc-editor.org/rfc/rfc8446)
- [HSTS, RFC 6797](https://www.rfc-editor.org/rfc/rfc6797)
- [DNS over HTTPS, RFC 8484](https://www.rfc-editor.org/rfc/rfc8484)
- [cURL certificate verification](https://curl.se/docs/sslcerts.html)
- [cURL manual](https://curl.se/docs/manpage.html)

Independent English study notes based on the supplied HTB Academy lesson, with technical clarifications and original examples.
