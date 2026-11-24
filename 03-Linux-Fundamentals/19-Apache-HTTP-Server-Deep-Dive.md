# Apache HTTP Server — Extended Study Notes (Section 19)

## What is Apache?
Apache HTTP Server is free, open-source web server software. It accepts HTTP requests and returns HTTP responses, serves static files, and can forward requests to application servers. With appropriate TLS configuration it also serves HTTPS. Apache is a web server, not a programming language, database, Git repository or operating system.

## How a web request works
1. A browser resolves the website hostname and connects to the server.
2. The browser sends an HTTP request, such as GET /.
3. Apache selects the appropriate virtual host and applies configuration and access rules.
4. Apache serves a static resource or forwards the request to an application backend.
5. The response contains an HTTP status, headers and usually a body; the browser renders the content.

Common status codes: 200 OK, 301 permanent redirect, 403 forbidden, 404 not found, 500 internal server error, 502 bad gateway. HTTP usually uses TCP port 80 and HTTPS TCP port 443. Changing the port does not enable TLS by itself.

## Apache, Git and application architecture
Git tracks source-code changes; GitHub hosts repositories; Apache serves requests. Flask implements Python application logic. A database stores records. Linux hosts these components. Apache can serve static content directly and proxy requests to a production WSGI server running Flask, or integrate with Flask using mod_wsgi.

Illustrative UniTrack architecture (not a claim about the currently deployed system):

Browser -> HTTPS -> Apache -> production WSGI application server / Flask -> database

Apache is optional; other web servers or deployment platforms may provide equivalent functionality. Flask's development server is not recommended as a production server. An Apache software license in a Git repository is also distinct from Apache HTTP Server; it governs software use and distribution, not hosting.

## Ubuntu/Debian files
| Location | Purpose |
| --- | --- |
| /etc/apache2/apache2.conf | Main configuration |
| /var/www/html/ | Common default document root |
| /etc/apache2/sites-available/ | Available virtual host configurations |
| /etc/apache2/sites-enabled/ | Enabled virtual host configurations |
| /etc/apache2/mods-available/ | Available modules |
| /etc/apache2/mods-enabled/ | Enabled modules |
| /var/log/apache2/access.log | Common request log |
| /var/log/apache2/error.log | Common error log |

Paths and logs can vary with distribution and virtual host configuration.

## Installing and inspecting Apache
These are instructional commands for a learner-owned Linux environment. No installation has been performed in this lesson extension.

~~~bash
sudo apt update
sudo apt install apache2
apache2 -v
systemctl status apache2
~~~

sudo requests authorized administrative privileges; apt update refreshes package indexes; apt install retrieves the package and resolves dependencies; apache2 -v prints the version; systemctl status inspects the service. Alternative version check: apache2ctl -v. Review proposed package changes before approving them. On WSL, systemctl requires systemd to be running.

| Command | Purpose |
| --- | --- |
| sudo systemctl start apache2 | Start service now |
| sudo systemctl stop apache2 | Stop service |
| sudo systemctl restart apache2 | Stop and start service |
| sudo systemctl reload apache2 | Request configuration reload |
| sudo apache2ctl configtest | Validate configuration syntax |
| apache2ctl -S | Inspect virtual host configuration |

A syntax check does not prove a configuration is secure. Avoid modifying services on a shared or assigned lab target unnecessarily.

## Safe local verification
If Apache is already installed and running on the learner-owned Ubuntu system:
~~~bash
curl -I http://127.0.0.1/
~~~
-I requests HTTP headers; 127.0.0.1 is the IPv4 loopback address. This tests local connectivity without probing an external host. Alternative: curl http://127.0.0.1/ to read the response body. Expected behavior depends on local configuration; neither command has been executed in this extension.

## Apache configuration concepts
A virtual host associates website settings with a hostname or address. DocumentRoot specifies a directory serving website files. Directory directives determine applicable access and behavior. Options Indexes permits directory listing; FollowSymLinks permits symbolic-link traversal subject to configuration. AllowOverride controls supported .htaccess overrides. Require all granted permits access to the relevant resource. Modules extend functionality, such as TLS and reverse proxying.

## Security considerations
Use supported software versions, least-privilege file permissions, correctly configured TLS, restricted document roots, minimal enabled modules and appropriate access logging. Review directory listing, virtual host and access-control configuration in authorized environments. HTTP request logs can help identify unusual activity, but should be protected because they may include sensitive information. Never publish credentials, private configuration, session identifiers or authentication logs.

## Comparison
Apache HTTP Server, Nginx and Caddy are general-purpose open-source web servers. Python's http.server is intended primarily for temporary development and is not a replacement for a production web server.

## Verification
This is a supplemental conceptual explanation requested by the learner. Commands and example architecture are illustrative, not verified executions or changes to UniTrack. Section 19 contains no mandatory questions; progress remains 19/30 studied.

Further reading: https://httpd.apache.org/docs/2.4/

Independent English study notes extending HTB Academy Section 19.
