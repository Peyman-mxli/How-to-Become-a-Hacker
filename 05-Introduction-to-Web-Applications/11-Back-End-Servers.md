# Section 11 — Back End Servers

**Module:** Introduction to Web Applications  
**Provider:** Hack The Box Academy  
**Study date:** November 26, 2026  
**Status:** Theory documented; required conceptual question reasoned from the supplied lesson. Platform acceptance pending.

## 1. Server architecture

A back-end server is a physical or virtual computing environment that executes application processes. Its operating system manages hardware, memory, storage, networking and process scheduling. A server may host several services, or each service may run on separate machines.

A host supplies computing resources; a process is a running program; a service is functionality offered to clients. A technology stack is a collection of components selected to work together. The data access layer (DAL) is application code that reads and writes persistent data, not the physical server itself. A server can host several logical application layers.

```text
Browser -- HTTPS --> Web server / reverse proxy
                          |
                    Application logic
                          |
                    Data access layer
                          |
                       Database
```

Responses travel back through the appropriate components. This is an illustrative architecture, not a required topology.

## 2. Software components

| Component | Purpose | Examples |
| --- | --- | --- |
| Operating system | Manages computing resources | Linux, Windows Server |
| Web server | Receives HTTP requests, serves files or proxies requests | Apache HTTP Server, NGINX, IIS |
| Application framework | Routes requests, supports validation and business logic | Flask, Django, Laravel, ASP.NET Core, Spring Boot, Express |
| Database engine | Stores, queries and manages persistent data | PostgreSQL, MySQL, MariaDB, SQL Server, SQLite, Oracle Database |

PHP, C# and Java are languages, not frameworks. IIS means Internet Information Services. A reverse proxy receives requests on behalf of upstream services, and may terminate TLS. A database is not required for every static website.

### Individual web server guides

- [Apache HTTP Server](Web-Servers/01-Apache-HTTP-Server.md)
- [NGINX](Web-Servers/02-NGINX.md)
- [Microsoft IIS](Web-Servers/03-Microsoft-IIS.md)
- [Comparison and reading index](Web-Servers/README.md)

These English-only supplements expand architecture, configuration, commands, original local hosting examples, security, troubleshooting and project applications. They do not add HTB lesson credit or confirm exercise acceptance.

## 3. Common stacks

| Stack | Operating environment | Web server | Data engine | Application technology |
| --- | --- | --- | --- | --- |
| LAMP | Linux | Apache | MySQL (or MariaDB variant) | PHP |
| WAMP | Windows | Apache | MySQL (or MariaDB variant) | PHP |
| WINS | Windows | IIS | SQL Server | .NET |
| MAMP | macOS in the conventional acronym | Apache | MySQL | PHP |
| XAMPP | Cross-platform | Apache | MariaDB (historically MySQL) | PHP and Perl |

The acronym indicates a conventional combination, not a strict interoperability rule. The first character of the Windows-oriented stack identifies its operating environment. MAMP is also a product available for Windows; modern XAMPP packages generally bundle MariaDB. These stacks are examples, not an exhaustive list. A development bundle is not automatically a hardened production deployment.

## 4. Virtualization, containers and filtering

- A hypervisor provides virtual hardware to guest virtual machines (VMs), each generally running its own OS kernel. KVM and VirtualBox are examples; Hyper-V is a Windows virtualization technology.
- A container packages an application and dependencies, isolates processes and shares the kernel of the container host. Docker Engine and Podman are examples. Docker Desktop on Windows generally uses a Linux VM or WSL2 for Linux containers.
- A web application firewall (WAF) inspects HTTP traffic according to rules and policies. ModSecurity and OWASP Core Rule Set are examples. A WAF is defense in depth, not a replacement for secure code, authorization or patching.
- TLS (Transport Layer Security) encrypts data in transit when HTTPS is correctly configured; it does not fix application authorization flaws.

## 5. Hardware and scalability

| Resource | Role |
| --- | --- |
| CPU (Central Processing Unit) | Executes instructions |
| RAM (Random Access Memory) | Holds working data and active processes |
| SSD/HDD | Persistent storage; SSDs have no moving parts |
| NIC (Network Interface Card) | Connects the host to a network |
| GPU | Accelerates suitable parallel workloads |
| PSU | Supplies power to physical equipment |

Vertical scaling increases resources on one machine. Horizontal scaling adds instances. A load balancer distributes traffic; health checks detect unhealthy instances. Redundancy duplicates resources, failover switches to healthy resources and high availability seeks to reduce downtime. A data center provides physical hosting infrastructure; cloud hosting provides computing resources through a provider. Multiple application servers alone do not eliminate database or network single points of failure.

## 6. Original example: inspect an owned development environment

These are illustrative read-only commands, not required HTB exercises. Run only against your own environment. No installation is needed for the conceptual lesson.

Ubuntu WSL2:
```bash
cat /etc/os-release
uname -m
free -h
df -h
ss -ltn
curl -I http://127.0.0.1:5000/
```

- cat reads the OS identification file; look at NAME or PRETTY_NAME for the distribution.
- uname -m prints the machine architecture.
- free -h displays memory in human-readable units.
- df -h shows filesystem capacity in human-readable units.
- ss -ltn lists listening (-l) TCP (-t) sockets with numeric addresses and ports (-n).
- curl -I issues an HTTP HEAD request; 127.0.0.1 is the local loopback interface, 5000 is the example application port, and / is the root path. It requires a running local service and a reachable network namespace. A Server header, if present, is only a clue and can be altered or omitted.

Windows PowerShell:
```powershell
Get-CimInstance Win32_OperatingSystem | Select-Object Caption, Version
Get-NetTCPConnection -State Listen
```

The first command queries OS metadata and selects the name/version fields; the second lists listening TCP connections. The pipe passes objects to Select-Object. Example output is not claimed as observed.

## 7. Original project applications

### UniTrack (Flask)

A local development setup may use a Windows host, Python, Flask's development server and its configured database. A possible production Linux deployment is NGINX -> Gunicorn WSGI server -> Flask -> configured database. Gunicorn is a Python application server for Unix-like systems; WSGI defines the interface between compatible Python servers and applications. Keep the database private, disable debug mode, use secure session cookies, enforce roles on the server, and back up attendance data. Do not treat the Flask development server as a production server.

### ProTrack-MX (React and Supabase)

The React/Vite client runs in the browser after delivery by a host. Supabase supplies backend APIs, authentication and PostgreSQL services. The client should not be trusted to enforce access control. Configure row-level security (RLS), validate server-side permissions and never embed privileged service-role credentials in frontend bundles. These are design recommendations, not a verified deployment audit.

### Original capacity scenario

If a school application receives a burst of attendance requests, two application instances behind a load balancer can improve capacity if the database and network can handle the traffic. Share session state or use compatible session handling, preserve atomic attendance transactions and monitor database connections. Scaling does not automatically fix incorrect access logic or race conditions.

## 8. Security risks and controls

| Risk | Defensive control |
| --- | --- |
| Outdated OS or web server | Track supported versions and apply security patches |
| Public database/admin ports | Restrict network access and firewall rules |
| Default credentials or insecure secrets | Rotate credentials and use least privilege |
| Debug output or exposed configuration | Disable debug mode and keep secrets outside public files |
| Misconfigured containers | Use trusted images, non-root users and limited privileges |
| Resource exhaustion | Rate limits, monitoring, capacity limits and load management |
| Weak tenant isolation | Enforce server-side authorization and database policies |
| Missing recovery procedures | Test backups, restoration and failover |

A technology fingerprint can help an authorized assessor choose relevant checks, but headers and stack names do not prove a vulnerability. Stay within explicit scope.

## 9. Required question and verification

The supplied section includes one conceptual question about the operating environment associated with a named stack. Its reasoning is obtained directly from the lesson's software-stack table by locating the matching row and identifying the OS component. No Pwnbox, target, command execution or installation is necessary. Restricted assessment submission text is not recorded separately here. Platform submission and acceptance have not yet been confirmed by the learner.

## References

- [HTB Academy — Introduction to Web Applications](https://academy.hackthebox.com/course/preview/introduction-to-web-applications)
- [Apache HTTP Server documentation](https://httpd.apache.org/docs/)
- [NGINX documentation](https://nginx.org/en/docs/)
- [Apache Friends — XAMPP](https://www.apachefriends.org/)
- [Docker documentation](https://docs.docker.com/)
- [OWASP Core Rule Set](https://coreruleset.org/)
- [Flask deployment documentation](https://flask.palletsprojects.com/en/stable/deploying/)
- [Supabase row-level security](https://supabase.com/docs/guides/database/postgres/row-level-security)

All examples are independently written and illustrative. No live testing, restricted answers, private credentials or target addresses are included.
