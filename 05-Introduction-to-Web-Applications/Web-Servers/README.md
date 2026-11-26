# Web Servers — Apache, NGINX and IIS

Supplement to [Section 11 — Back End Servers](../11-Back-End-Servers.md) and [Section 12 — Web Servers](../12-Web-Servers.md). Read in order:

1. [Apache HTTP Server](01-Apache-HTTP-Server.md)
2. [NGINX](02-NGINX.md)
3. [Microsoft IIS](03-Microsoft-IIS.md)
4. [Apache Tomcat](04-Apache-Tomcat.md) — Java servlet container and HTTP service.
5. [Node.js](05-NodeJS.md) — JavaScript runtime and HTTP server API.

## Comparison

| Feature | Apache HTTP Server | NGINX | IIS |
| --- | --- | --- | --- |
| License | Apache 2.0 | BSD-style open-source edition | Proprietary Windows feature |
| Typical environment | Linux or Windows | Linux/Unix-like | Windows |
| Configuration | httpd directives, optional .htaccess | nginx directives and contexts | XML, IIS Manager, AppCmd |
| Concurrency | Selected MPM | Event-driven workers | HTTP.sys and worker processes |
| Static hosting | Yes | Yes | Yes |
| Reverse proxy | mod_proxy | Built-in HTTP proxy module | Additional proxy tooling such as ARR for general reverse proxy |
| Application integration | PHP-FPM, mod_wsgi, upstreams | FastCGI, HTTP upstreams | ASP.NET Core Module, compatible handlers |
| Typical logs | access.log/error.log | access.log/error.log | W3C per-site logs, HTTPERR, Event Viewer |

No universal fastest or most secure server exists; workload, configuration, patching and deployment requirements determine suitability. Apache and NGINX are free/open-source choices; IIS availability and Windows licensing depend on edition. For a Flask project on Linux, a production WSGI server plus NGINX or Apache is a common design; for a Windows/.NET intranet, IIS is a natural option. All three can serve a React build, and Supabase can supply backend services independently.

Each guide includes architecture, configuration terms, original local examples, command explanations, expected behavior, troubleshooting, defenses and project applications. Examples use loopback addresses and are not mandatory labs. No HTB progress credit or test execution is claimed for these supplements.
