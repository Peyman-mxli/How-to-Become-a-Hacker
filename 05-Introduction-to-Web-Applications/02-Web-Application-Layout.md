# Lesson 02/17 — Web Application Layout

## Learning objectives
Distinguish infrastructure, components and architecture; compare deployment models; explain three-tier architecture, microservices, serverless computing and architecture-level security.

## Layout categories
Infrastructure describes where resources are hosted and connected. Components are the parts that perform application tasks, including the UI, client, server, data stores and integrations. Architecture defines their relationships, responsibilities and communication boundaries.

## Infrastructure models

| Model | Characteristics | Main considerations |
| --- | --- | --- |
| Client-server | Clients communicate with a server over a network | General interaction model; compatible with the other deployment models |
| One server | Application and database share a host | Simple and inexpensive; shared failure domain and potential compromise impact |
| Many servers, one database | Application instances or distinct applications use a separate shared database | Segmentation, shared data, database bottleneck or failure risk |
| Many servers, many databases | Applications have separate databases or data boundaries | Isolation and independent scaling; additional operational complexity |

A single application server with a separately hosted database also fits the separated-database model. Logical databases may share a physical database server. Separation alone does not guarantee security: permissions, network restrictions, process isolation and credential management determine the actual impact of compromise. Multiple databases do not automatically provide replication or failover. Separate database backups and tested recovery procedures are needed.

A load balancer distributes incoming requests among healthy application instances. Redundancy means providing alternative components to continue service when one fails. Replication copies data; failover redirects work to a replacement component. These mechanisms are distinct and must be configured and tested.

## Components
- Client: browser or other application consuming the service.
- UI/UX: user interface and user experience.
- Web server: accepts HTTP requests, serves static content or forwards requests to application logic.
- Web application logic: validates requests, enforces access policies and implements business operations.
- Database: persists and retrieves data.
- Services: independent internal services and third-party integrations.
- Functions: event-triggered or request-triggered serverless compute.

A reverse proxy can receive public traffic and forward it to an internal application. Some platforms support server-side integrations such as IIS ISAPI extensions and PHP-CGI. These are distinct from browser-side code and must be configured securely.

## Three-tier architecture

| Tier | Responsibility |
| --- | --- |
| Presentation | User-facing interface, HTML/CSS/JavaScript and communication with application |
| Application | Request processing, authentication, authorization, validation and business logic |
| Data | Storage, retrieval, data-access rules and persistence |

Logical tiers need not be on separate physical servers. For example, a Flask application with templates and a local SQLite file may implement three logical layers on one host. A distributed system may deploy each layer separately.

## Microservices
Microservices organize an application into independently deployable services that focus on business capabilities. An online store may separate registration, search, payments, ratings and reviews. Services can use different languages and communicate through documented HTTP APIs or asynchronous messaging. Their boundaries can enable independent scaling, deployment and fault isolation.

Technical clarification: stateless request handling is a common design goal, not a requirement that every microservice be stateless. Persistent data can reside in databases or other durable stores. Services can have dependencies, so resilience requires timeouts, failure handling, monitoring and careful design. Distributed services introduce authentication, authorization and operational complexity.

## Serverless
Serverless platforms abstract infrastructure provisioning and management. Examples include AWS Lambda, Azure Functions and Google Cloud Run functions. Functions may execute in managed environments in response to HTTP requests, schedules or events. Servers still exist and the provider manages much of the underlying infrastructure. Docker containers are one possible implementation, not a universal requirement. Application developers retain responsibility for code security, permissions, secrets, data and configuration. Serverless does not necessarily mean free or unlimited.

## Architecture security
Security defects may arise from architectural choices rather than individual programming mistakes. Role-based access control (RBAC) maps roles to allowed operations. Object-level authorization is also required when a role alone does not determine whether a particular record belongs to the requester.

Design principles:
- Least privilege for service identities, database roles and users.
- Network segmentation between public application endpoints and private data systems.
- Server-side authorization on every protected operation.
- Explicit trust boundaries, protected service-to-service communication and secret management.
- Backups, tested restoration, monitoring and controlled failover.
- Security review throughout the development lifecycle and authorized retesting after changes.

## Original example: school application deployment
Consider a fictional school system inspired by UniTrack. This example is a design proposal, not an audit of the actual repository.

Development model: browser -> local Flask application -> local database.
Distributed model: browser -> reverse proxy -> application servers -> private database.
A notification service may send email independently; a background function may process an authorized report event.

A student can view their own attendance, a teacher can access assigned classes, and an administrator can manage authorized institution data. The server must enforce these distinctions. A database account used by a read-only reporting service should not have unrestricted write permissions.

Illustrative local diagnostic commands in Ubuntu WSL2, assuming an owned application is running:
~~~bash
curl -i http://127.0.0.1:5000/
ss -ltn
~~~
curl -i displays response headers along with the body; the URL refers to the loopback interface. ss -ltn lists listening TCP sockets numerically: -l listening, -t TCP, -n numeric addresses and ports. An illustrative listening address is 127.0.0.1:5000. A listening socket does not prove that a database exists or that its access controls are correct. No command was executed; no new installation or practice is required.

## Verification
Section 2 theory studied and documented. No mandatory questions in the supplied lesson. Optional exercises skipped; no actual deployment or security assessment performed. HTB section completion not separately confirmed.

## References
- [HTB Academy — Introduction to Web Applications](https://academy.hackthebox.com/app/module/75)
- [Microsoft — Common web application architectures](https://learn.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)
- [AWS — Microservices](https://aws.amazon.com/microservices/)
- [AWS — Serverless](https://aws.amazon.com/serverless/)
- [OWASP — Authorization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html)
- [OWASP — Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)

Independent English notes based on the supplied lesson, with original examples and clearly identified technical clarifications.
