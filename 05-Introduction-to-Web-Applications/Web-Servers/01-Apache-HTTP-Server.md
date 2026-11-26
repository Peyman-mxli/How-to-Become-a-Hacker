# Apache HTTP Server — Complete Study Guide

**Section:** 11 — Back End Servers (supplement)  
**Type:** Independent technical reference  
**License:** Apache License 2.0; free and open-source

## 1. What Apache is and why it exists

Apache HTTP Server (httpd) is a general-purpose HTTP server maintained by the Apache Software Foundation. It accepts network connections, parses HTTP requests, selects a virtual host and handler, and returns HTTP responses. It can deliver static files, invoke application handlers, or forward requests to another server. It is available on Linux, Windows and other supported systems. Apache is not PHP, a database, a programming language or the Apache Software Foundation itself. A single physical server may run Apache alongside application and database services.

A typical flow:

```text
Browser -> TCP/TLS -> Apache -> virtual host -> handler
                                               |-- static file
                                               |-- PHP-FPM or WSGI app
                                               |-- reverse proxy upstream
Browser <- HTTP status, headers and body <------+
```

HTTP is the application protocol; HTTPS uses TLS for transport protection. A listening socket binds an address and port. DNS resolves hostnames; it does not itself route requests to filesystem paths. The HTTP Host header helps choose a virtual host. A document root is the public filesystem directory, not the entire application directory. An origin server generates or serves content; an upstream is a service to which a proxy forwards traffic.

## 2. Architecture and concurrency

Apache uses a core plus loadable modules. A directive configures behavior, and a configuration section defines scope (server, virtual host, directory, location). MPM means Multi-Processing Module: prefork uses child processes without worker threads, worker uses processes and threads, event builds on worker and handles idle keep-alive connections more efficiently, and winnt uses Windows-specific mechanisms. Only one MPM is active per server instance. Actual performance depends on workload, configuration, operating system and upstream latency.

| Module | Purpose |
| --- | --- |
| mod_ssl | TLS and HTTPS |
| mod_proxy / mod_proxy_http | HTTP reverse proxy |
| mod_proxy_fcgi | FastCGI connections such as PHP-FPM |
| mod_rewrite | Rule-based URL rewriting |
| mod_headers | HTTP header management |
| mod_alias | URL-to-file mappings and redirects |
| mod_autoindex | Optional generated directory listings |
| mod_auth_basic / mod_authz_core | Authentication and authorization mechanisms |
| mod_log_config | Request log formatting |
| mod_status | Operational status, restrict to administrators |
| mod_security2 | Separately installed WAF module; not enabled by default |
| mod_wsgi | Separately installed Python WSGI integration |

Authentication verifies identity; authorization determines allowed actions. Basic authentication sends encoded rather than encrypted credentials and requires TLS for real use.

## 3. Common Ubuntu/Debian paths

| Path | Meaning |
| --- | --- |
| /etc/apache2/apache2.conf | Main configuration |
| /etc/apache2/ports.conf | Listen directives |
| /etc/apache2/sites-available/ | Available site files |
| /etc/apache2/sites-enabled/ | Enabled site links |
| /etc/apache2/mods-available/ and mods-enabled/ | Available/enabled modules |
| /var/www/html/ | Typical default document root |
| /var/log/apache2/access.log | Typical request log |
| /var/log/apache2/error.log | Typical diagnostic log |

Other distributions often use /etc/httpd and the httpd service name. Windows paths differ. .htaccess is a per-directory configuration file that works only where AllowOverride permits it; prefer controlled central configuration when possible.

## 4. Original example: a local school website

Prerequisites: Apache installed, a readable directory /var/www/school, an index.html file, a free port and a matching Listen directive. Example HTML:

```html
<!doctype html>
<html lang="en">
<head><meta charset="utf-8"><title>School Portal</title></head>
<body><h1>School Portal</h1><p>Static demonstration page.</p></body>
</html>
```

In ports.conf (only once):

```apache
Listen 127.0.0.1:8080
```

Example sites-available/school.conf:

```apache
<VirtualHost 127.0.0.1:8080>
    ServerName localhost
    DocumentRoot /var/www/school
    <Directory /var/www/school>
        Options -Indexes
        AllowOverride None
        Require all granted
    </Directory>
    ErrorLog ${APACHE_LOG_DIR}/school-error.log
    CustomLog ${APACHE_LOG_DIR}/school-access.log combined
</VirtualHost>
```

Listen opens the local socket; VirtualHost selects configuration for that address and port; ServerName names the host; DocumentRoot maps URLs to public files; Directory sets filesystem-specific rules; Options -Indexes removes directory listings; AllowOverride None disables .htaccess overrides; Require all granted permits access inside that directory; ErrorLog and CustomLog select log paths; combined is a predefined access-log format on typical Ubuntu installations.

With a dedicated lab site, an administrator could enable it using sudo a2ensite school.conf, check syntax with sudo apache2ctl configtest, and reload with sudo systemctl reload apache2. a2ensite enables a site; sudo runs the command with elevated privileges; configtest validates configuration syntax; reload applies configuration while retaining the service process where supported. Systemd must be active for systemctl. Do not overwrite existing sites or duplicate Listen entries.

```bash
curl -i http://127.0.0.1:8080/
```

-i includes response headers and body; the loopback address limits the connection to the local network namespace. Expected illustrative response: HTTP 200 and the School Portal HTML when correctly configured.

## 5. Original example: reverse proxy to Flask

Use a separate site or replace the preceding example only in a disposable lab. Requires mod_proxy and mod_proxy_http (Ubuntu: sudo a2enmod proxy proxy_http), the matching Listen directive and a running local app.

```apache
<VirtualHost 127.0.0.1:8080>
    ServerName localhost
    ProxyRequests Off
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
</VirtualHost>
```

ProxyRequests Off disables forward-proxy mode; ProxyPreserveHost forwards the incoming Host header, so validate acceptable hosts in production; ProxyPass maps incoming paths to an upstream; ProxyPassReverse adjusts certain upstream redirect headers. It does not rewrite every link in response bodies. A production Flask deployment needs a supported WSGI server or mod_wsgi rather than Flask's development server. WSGI is the Python Web Server Gateway Interface. Both services must be reachable from the proxy's network namespace.

## 6. Administration and troubleshooting

| Command | Explanation |
| --- | --- |
| apache2 -v | Print server version |
| sudo apache2ctl -M | List loaded modules |
| sudo apache2ctl -S | Show virtual host mapping |
| sudo apache2ctl configtest | Check syntax |
| sudo systemctl status apache2 | Inspect service status |
| sudo systemctl reload apache2 | Apply validated config |
| sudo tail -n 30 /var/log/apache2/error.log | Display last 30 diagnostic lines |
| curl -I http://127.0.0.1:8080/ | Send HEAD; display response headers |

tail -n selects the number of final lines; -I requests HEAD instead of GET. A 403 may indicate access rules or filesystem permissions; 404 may indicate an incorrect root or route; 502/503 may indicate upstream failure. Check the selected vhost, logs, service state and upstream availability before changing configuration. A Server header may be hidden or modified; it is not definitive proof of software version or vulnerability.

## 7. Security and authorized assessment

- Patch Apache, its OS and modules using supported packages.
- Use TLS with trusted certificates for real deployments; protect private keys.
- Store backups, .env files, repositories and database files outside the public root. Directory listing disabled does not make known file URLs private.
- Use minimal modules, least-privileged process identities and correct filesystem permissions. Never grant broad write permissions just to fix a 403.
- Restrict status endpoints, admin interfaces and upstream ports; disable forward proxying unless explicitly needed.
- Validate uploaded files and prevent execution in upload directories. Do not trust a filename extension alone.
- Set appropriate request size/time limits; monitor errors and rotate logs.
- Review configuration and package inventory on owned systems. Test only authorized URLs. Product banners are clues, not proof of exploitable vulnerabilities.
- A WAF adds a layer of protection but cannot enforce all business permissions or replace secure application code.

## 8. Project applications

UniTrack can use Apache as a TLS-terminating reverse proxy in front of a production WSGI server, or use mod_wsgi with a supported Python deployment. Keep attendance data and credentials outside the public root and enforce authorization in Flask. ProTrack-MX could serve the compiled React dist directory using Apache, while Supabase provides its external API and database. A single-page app may require a controlled fallback to index.html for client routes; exclude real API and asset paths from inappropriate rewriting. Do not expose privileged Supabase service-role keys.

## 9. References

- [Apache HTTP Server documentation](https://httpd.apache.org/docs/2.4/)
- [Virtual hosts](https://httpd.apache.org/docs/2.4/vhosts/)
- [Multi-processing modules](https://httpd.apache.org/docs/2.4/en/mpm.html)
- [Reverse proxy guide](https://httpd.apache.org/docs/2.4/howto/reverse_proxy.html)
- [Security tips](https://httpd.apache.org/docs/2.4/misc/security_tips.html)
- [Flask deployment](https://flask.palletsprojects.com/en/stable/deploying/)

## Verification and scope

This guide contains original educational examples. Commands and configurations are illustrative and have not been executed against the learner's machine or an HTB target. Use only owned or explicitly authorized environments. Do not install services or change project deployments merely to complete Section 11. Do not expose lab services to the Internet. Avoid publishing full logs, configuration dumps, credentials or student data.
