# NGINX — Complete Study Guide

**Section:** 11 — Back End Servers (supplement)  
**Type:** Independent technical reference  
**License:** BSD-style license for the open-source edition; commercial editions also exist.

## 1. Definition and uses

NGINX (engine x) is an HTTP server, reverse proxy, cache and load balancer. It also supports other network proxy modes through appropriate modules. It is primarily deployed on Linux and Unix-like systems; a Windows build exists with documented limitations. It is not a database or application framework and does not execute Python or PHP business logic by itself.

A forward proxy acts for clients; a reverse proxy acts for servers. A reverse proxy can provide a single public entry point, terminate TLS, apply request limits and route traffic to internal applications. TLS termination decrypts traffic at the proxy; separate upstream encryption may be needed across untrusted networks.

## 2. Architecture

The master process reads configuration and manages workers; workers handle network connections with event-driven, nonblocking I/O. I/O means input/output; nonblocking means the process need not wait on one connection before handling other ready connections. A worker may serve many connections; performance still depends on CPU, file descriptors, upstream speed and workload.

```text
Client -> NGINX master-managed workers -> static files
                                     -> application upstream
                                     -> cached response, if configured
```

Configuration uses directives and contexts. Main context contains global settings; events controls connection processing; http configures HTTP services; server defines a virtual server; location selects a URL pattern. An upstream block defines a group of backend servers. A root directive appends the URI to a filesystem path; alias replaces the matched location portion.

## 3. Common Ubuntu paths

| Path | Role |
| --- | --- |
| /etc/nginx/nginx.conf | Main configuration |
| /etc/nginx/conf.d/ | Included configuration fragments |
| /etc/nginx/sites-available/ | Available sites on Debian/Ubuntu |
| /etc/nginx/sites-enabled/ | Enabled sites on Debian/Ubuntu |
| /var/www/html/ | Common document root |
| /var/log/nginx/access.log | Request log |
| /var/log/nginx/error.log | Error log |

Paths and included files vary by installation method and operating system. Inspect the actual config; do not assume every installation includes sites-enabled.

## 4. Original static site

Assume a local-only lab, a readable /var/www/school/index.html and a free port.

```nginx
server {
    listen 127.0.0.1:8080;
    server_name localhost;
    root /var/www/school;
    index index.html;
    autoindex off;
    location / {
        try_files $uri $uri/ =404;
    }
}
```

listen defines address and port; server_name selects host; root maps URI to files; index selects a default filename; autoindex off disables generated directory listings; location / matches a URL prefix; try_files checks the requested file, then directory, then returns 404 if neither is found. $uri is the normalized URI path. Do not store private data in the public root.

In an Ubuntu package layout, a site may be enabled with a symlink from sites-available to sites-enabled, if included by nginx.conf. Always test with sudo nginx -t before reload. Example GET:

```bash
curl -i http://127.0.0.1:8080/
```

-i prints response headers and body. Expected illustrative result: HTTP 200 and the static page, if the file exists and permissions permit it.

## 5. Original Flask reverse proxy

Use a separate local site or replace the previous block in a disposable lab. A Flask app must be running and reachable from NGINX.

```nginx
server {
    listen 127.0.0.1:8080;
    server_name localhost;
    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_connect_timeout 5s;
        proxy_read_timeout 30s;
    }
}
```

proxy_pass forwards requests; proxy_set_header overwrites upstream request headers; $host is the selected request hostname with fallback; $remote_addr identifies the immediate connecting peer; $scheme is the incoming scheme. proxy_connect_timeout limits upstream connection establishment; proxy_read_timeout controls the interval between upstream read operations, not the entire request duration. The backend must trust only known proxies and the correct number of forwarded hops; validate hostnames. Do not blindly trust externally supplied X-Forwarded-For or X-Forwarded-Proto headers. Local HTTP is illustrative; real deployments need appropriate TLS and a production application server.

## 6. Load balancing

```nginx
upstream school_backend {
    server 127.0.0.1:5001;
    server 127.0.0.1:5002;
}
server {
    listen 127.0.0.1:8080;
    location / {
        proxy_pass http://school_backend;
    }
}
```

upstream defines a named server group. Default round-robin distributes requests across available upstreams; least_conn can prefer servers with fewer active connections. Availability and failure behavior depend on configuration. Both processes need shared or compatible session handling, persistent data and atomic operations. Avoid placing separate unsynchronized SQLite files behind a load balancer. Passive failure handling is not equivalent to every feature of commercial active health checks.

## 7. Original React SPA hosting example

```nginx
server {
    listen 127.0.0.1:8080;
    root /var/www/protrack/dist;
    index index.html;
    location /assets/ {
        try_files $uri =404;
    }
    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

Vite builds static assets into dist. A React single-page application (SPA) handles client-side routes; fallback to index.html enables direct navigation to those routes. The separate assets location prevents missing asset files from returning HTML. Real API routes need independent routing rather than SPA fallback. Supabase provides backend services; this example does not run Supabase itself. Never put a service-role credential into the frontend build.

## 8. Administration and troubleshooting

| Command | Meaning |
| --- | --- |
| nginx -v | Version |
| nginx -V | Version, build and compile options |
| sudo nginx -t | Validate syntax and referenced files |
| sudo nginx -T | Test and dump loaded configuration; may reveal secrets |
| sudo systemctl status nginx | Service state when systemd is enabled |
| sudo systemctl reload nginx | Apply validated configuration |
| sudo tail -n 30 /var/log/nginx/error.log | Last 30 error-log lines |
| curl -I http://127.0.0.1:8080/ | HEAD request and headers |

A 502 can indicate an inaccessible or invalid upstream response, 504 can indicate an upstream timeout, and 404 can indicate wrong root or route. Check listening sockets, server/location matching, logs and upstream availability. Do not disable security rules indiscriminately.

## 9. Security, caching and rate limits

- Patch NGINX and its OS; use trusted packages and nonprivileged workers.
- Restrict administrative and upstream network exposure; use HTTPS for real traffic.
- Set appropriate timeouts and request-body limits. Define request-rate zones with limit_req_zone in http context and apply limit_req selectively to appropriate endpoints; account for trusted proxy addresses and legitimate bursts.
- Do not cache authenticated dashboards, grades or attendance data in shared caches. Honor privacy-aware cache policies; test with separate authorized accounts.
- Review location and alias path mappings; deny access to confidential files and keep them outside the document root.
- Avoid excessive error detail. Logs may contain session or personal data; restrict access and retention.
- Use trusted proxy headers and application-side authorization. A proxy does not itself fix CSRF, XSS, SQL injection or broken access control.
- WebSockets require suitable proxy configuration (HTTP upgrade handling) when the app uses them; a standard HTTP proxy block may not be sufficient.
- Use authorized inventory and read-only configuration review; a response header alone cannot confirm a vulnerability.

## 10. Applications to our projects

UniTrack: NGINX can terminate TLS and proxy to Gunicorn/Flask. Multiple app processes need consistent session secrets, correct proxy trust and a suitable transactional database. ProTrack-MX: NGINX can deliver the React frontend and optionally route an owned API, while Supabase provides authentication and PostgreSQL. Configure RLS and do not trust client-side role checks. These are candidate architectures, not actual deployment changes.

## References

- [NGINX official overview](https://nginx.org/en/)
- [Beginner's guide](https://nginx.org/en/docs/beginners_guide.html)
- [Request processing](https://nginx.org/en/docs/http/request_processing.html)
- [Proxy module](https://nginx.org/en/docs/http/ngx_http_proxy_module.html)
- [Upstream module](https://nginx.org/en/docs/http/ngx_http_upstream_module.html)
- [Request limiting](https://nginx.org/en/docs/http/ngx_http_limit_req_module.html)

## Verification and scope

This guide contains original educational examples. Commands and configurations are illustrative and have not been executed against the learner's machine or an HTB target. Use only owned or explicitly authorized environments. Do not install services or change project deployments merely to complete Section 11. Do not expose lab services to the Internet. Avoid publishing full logs, configuration dumps, credentials or student data.
