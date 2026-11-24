# Lesson 20/30 — Working with Web Services

## Apache modules
Apache is modular and can serve static content or integrate with application runtimes to generate dynamic responses.

| Module | Purpose |
| --- | --- |
| mod_ssl | TLS/HTTPS support |
| mod_proxy | Forward requests to other services |
| mod_headers | Control HTTP headers |
| mod_rewrite | Rewrite URLs |

## Apache installation and port configuration
~~~bash
sudo apt install apache2
sudo systemctl start apache2
~~~
APT installs software; systemctl starts the service. The default HTTP port is 80. If occupied, configure an available port in /etc/apache2/ports.conf and update the appropriate virtual host. Example: Listen 8080. Check configuration with sudo apache2ctl configtest before restarting. These are instructional commands, not changes made to the learner's system.

## HTTP client tools
~~~bash
curl http://localhost:8080
curl -I http://localhost:8080
wget http://localhost:8080
~~~
curl normally displays the response body. -I requests headers using HTTP HEAD. wget normally saves the response to a local file. Other curl options: -v verbose communication, -L follow redirects, -o output filename, -s silent progress.

Common HTTP status codes: 200 OK, 301 permanent redirect, 400 bad request, 401 authentication required, 403 forbidden, 404 not found, 500 internal server error, 503 service unavailable.

## Python HTTP server
~~~bash
python3 -m http.server 8080 --bind 127.0.0.1
~~~
-m runs the built-in module; 8080 sets the port; --bind restricts access to IPv4 loopback. Use a dedicated practice directory containing no sensitive files. Python's simple HTTP server is intended primarily for development and temporary use.

## Mandatory question 1 — npm HTTP server on port 8080
The question requests the start command, using the short port argument, not the package installation command.

Installation if needed:
~~~bash
npm install -g http-server
~~~
npm is the Node.js package manager; install installs the package and -g selects global installation. The package provides the http-server executable.

Expected submission:
~~~bash
http-server -p 8080
~~~
-p is the short port option, and 8080 is the requested port. Alternative execution using npm's package runner:
~~~bash
npx http-server -p 8080
~~~
For local-only practice, run from a dedicated non-sensitive directory and add -a 127.0.0.1. Stop the server with Ctrl+C before starting another server on the same port.

The upstream package documentation verifies the port syntax, and HTB community reports corroborate the expected answer. Learner-side execution and HTB acceptance pending.

References:
- https://github.com/http-party/http-server
- https://forum.hackthebox.com/t/question-about-npm-on-linux-fundamentals-module-of-accademy/3522

## Mandatory question 2 — PHP HTTP server on localhost port 8080
Expected submission:
~~~bash
php -S 127.0.0.1:8080
~~~
php invokes the command-line interpreter; uppercase -S starts the built-in development server; 127.0.0.1 is IPv4 loopback; the colon separates address and port. PHP's development server is not for public production deployment.

Alternative for explicitly choosing a document root:
~~~bash
php -S 127.0.0.1:8080 -t public
~~~
This assumes a public directory exists and is not the exact requested submission.

Official reference: https://www.php.net/manual/en/features.commandline.webserver.php

## Optional local verification
On a learner-owned system, check npm --version and php --version to confirm availability. Start one server at a time in an isolated practice folder and use curl -I http://127.0.0.1:8080 from a second terminal to verify HTTP connectivity. Stop the server using Ctrl+C. These checks have not been executed by the learner.

## Verification
Two mandatory questions worth 40 HP. Both expected answers verified against upstream documentation, with community corroboration for question 1. HTB acceptance pending. No packages installed or servers started by the assistant. Section 19 had no mandatory questions and was followed by Section 20.

Independent English study notes based on HTB Academy.

The learner proceeded to Section 21; individual HTB submission acceptance was not explicitly shown.
