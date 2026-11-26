# Microsoft Internet Information Services (IIS) — Complete Study Guide

**Section:** 11 — Back End Servers (supplement)  
**Type:** Independent technical reference  
**Developer:** Microsoft  
**License:** Proprietary Windows component. It may be available without a separate IIS purchase on supported Windows editions; Windows licensing and feature availability still apply. IIS Express is a development-oriented alternative, not a production equivalent.

## 1. What IIS is

Internet Information Services (IIS) is Microsoft's web server for Windows. It delivers static files, hosts compatible applications, supports HTTP/HTTPS, authentication, request filtering and logging. It integrates with Windows services, identities and NTFS permissions. IIS is not the .NET runtime, SQL Server or a programming language. ASP.NET Core is a framework; C# is a language; Kestrel is an application web server. An appropriate ASP.NET Core Hosting Bundle is needed for IIS hosting of ASP.NET Core apps.

## 2. Architecture

```text
Client -> HTTP.sys (kernel HTTP listener)
       -> request queue -> w3wp.exe (IIS worker)
       -> modules / handlers -> static file or application
       -> response through HTTP.sys -> client
```

HTTP.sys handles HTTP traffic at the Windows kernel level. Windows Process Activation Service (WAS) manages worker process activation and application pools. World Wide Web Publishing Service (W3SVC) participates in HTTP listener configuration. w3wp.exe is the IIS worker process. The pipeline contains modules that process request events and handlers that generate responses for selected requests. An application pool provides process-level separation and a configurable identity; it is not a VM or a complete tenant security boundary.

| Term | Meaning |
| --- | --- |
| Site | Collection of web applications and bindings |
| Binding | Protocol, IP address, port and optional hostname |
| Application | URL-based application belonging to a site |
| Virtual directory | Maps a URL path to a physical directory |
| Application pool | Worker-process management and isolation configuration |
| Identity | Windows account or virtual account used by a worker |
| NTFS ACL | Filesystem access control list |
| Recycling | Replacement of worker processes according to conditions |
| Module | Component participating in the pipeline |
| Handler | Component responsible for serving a selected request |

ASP.NET Core in-process hosting runs the app within w3wp.exe; out-of-process hosting uses the ASP.NET Core Module (ANCM) to forward requests to Kestrel. The precise configuration depends on the hosting model. Windows Authentication is useful in appropriate intranet/domain deployments; it is not universally enabled or needed for public sites.

## 3. Common files and tools

| Path or tool | Meaning |
| --- | --- |
| C:\inetpub\wwwroot | Typical default site root |
| C:\Windows\System32\inetsrv\config\applicationHost.config | Central IIS configuration |
| web.config | Site/application-level XML configuration |
| C:\inetpub\logs\LogFiles\W3SVC<site-id> | Typical per-site access logs |
| C:\Windows\System32\LogFiles\HTTPERR | Typical HTTP.sys error logs |
| IIS Manager (inetmgr) | Graphical administration when installed |
| AppCmd.exe | Command-line IIS administration |
| Event Viewer | Windows service and application diagnostic events |

Paths are defaults and can be customized. XML uses nested elements and attributes; the configuration hierarchy controls inheritance and section locking.

## 4. Original example: local school website

Prerequisites: a supported Windows edition with IIS and management tools enabled, administrator privileges for configuration, an unused port and a readable public directory. No feature installation is required for this conceptual HTB section.

1. Create C:\inetpub\school-demo and add index.html with a simple School Portal heading.
2. In IIS Manager, create a dedicated application pool SchoolDemoPool with ApplicationPoolIdentity and No Managed Code for this static site.
3. Add a website SchoolDemo using that pool and physical path. Bind HTTP to 127.0.0.1, port 8080, with an empty hostname for this dedicated local example.
4. Give IIS AppPool\SchoolDemoPool only the necessary read/execute permissions on the public folder (check the site's anonymous authentication identity; if using application pool identity, configure it accordingly). Do not grant Everyone full control.
5. Open http://127.0.0.1:8080/ on the same Windows host.

Example web.config in the public root:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <directoryBrowse enabled="false" />
    <defaultDocument enabled="true">
      <files>
        <clear />
        <add value="index.html" />
      </files>
    </defaultDocument>
  </system.webServer>
</configuration>
```

configuration is the XML root; system.webServer contains IIS settings; directoryBrowse false disables directory listings; defaultDocument enables automatic default-file selection; clear removes inherited default-file entries in this collection; add sets index.html. Requires the corresponding installed IIS features and unlocked sections. A 500.19 may mean a configuration error, missing component or locked section. A successful GET would return HTTP 200 and the page; no observed result is claimed.

## 5. PowerShell and AppCmd examples

```powershell
Get-WindowsOptionalFeature -Online -FeatureName IIS-WebServerRole
Get-Service -Name W3SVC,WAS
& "$env:windir\System32\inetsrv\appcmd.exe" list sites
& "$env:windir\System32\inetsrv\appcmd.exe" list apppools
& "$env:windir\System32\inetsrv\appcmd.exe" list wp
curl.exe -I http://127.0.0.1:8080/
```

Get-WindowsOptionalFeature queries Windows features; -Online targets the currently running OS, not an Internet service; -FeatureName selects a feature. Get-Service lists named Windows services. The PowerShell call operator & executes the supplied program path; $env:windir expands the Windows directory; list sites shows sites/bindings; list apppools shows pools; list wp shows active worker processes. curl.exe explicitly selects the curl executable (avoids PowerShell alias ambiguity); -I performs HEAD. AppCmd requires IIS management tooling, typically an elevated shell; feature and service availability varies. An idle site may not yet have a worker process.

Illustrative log row (not observed):

```text
#Fields: date time c-ip cs-method cs-uri-stem sc-status sc-substatus time-taken
2026-11-26 08:30:00 127.0.0.1 GET / 200 0 4
```

c-ip is client address; cs-method is request method; cs-uri-stem is path; sc-status and sc-substatus identify result; time-taken is milliseconds in IIS W3C logs. Actual fields depend on logging configuration. Inspect #Fields before interpreting values.

## 6. ASP.NET Core hosting example

A minimal illustrative C# application:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("/", () => "School portal API");
app.Run();
```

WebApplication.CreateBuilder initializes configuration and services; Build creates the application; MapGet registers a GET route; Run starts processing. To host a real ASP.NET Core app in IIS, use a supported .NET release, install the corresponding hosting prerequisites, publish the project, configure a site pointing to published output and follow the official hosting model guidance. Do not expose source, development settings or secrets. The hosting module and generated web.config handle process integration; IIS does not compile and run arbitrary C# files simply because they are copied into a document root.

## 7. Troubleshooting and security

| Symptom | Possible cause / safe check |
| --- | --- |
| 401 | Authentication issue; check intended auth configuration |
| 403 | Authorization or access restrictions; inspect substatus and ACLs |
| 404 | Missing file, route or filtering decision; inspect substatus |
| 500.19 | Invalid/locked config or missing configuration module |
| 503 | App pool stopped or unavailable; check WAS and event logs |

Use individual least-privileged application pools; restrict NTFS write permission; disable unused modules, WebDAV and unnecessary authentication modes; use request filtering and upload limits; protect administrative interfaces; patch Windows, IIS and application runtimes; configure TLS and certificates; use custom production errors; protect applicationHost.config, web.config and secrets; keep sensitive files outside web roots; review logs without publishing personal data. Never solve a permission problem by assigning all applications administrator rights.

An authorized assessment may inspect owned site bindings, authentication modes, pool identities, runtime versions, request filtering, logs and patch state. Response headers can be modified and do not prove vulnerability.

## 8. Applications to our projects

UniTrack is Python/Flask, not ASP.NET Core. On Windows, a production-capable WSGI server such as Waitress could host Flask; IIS could reverse proxy to it only with appropriate additional proxy tooling and configuration. IIS is not required and this is not the currently verified deployment. ProTrack-MX could serve compiled React assets as an IIS static site, while Supabase remains the backend. A client-side routing fallback requires an appropriate rewrite module/rule; do not rewrite API or asset requests indiscriminately. RLS, server-side authorization and key protection remain required.

## References

- [Microsoft IIS architecture](https://learn.microsoft.com/en-us/iis/get-started/introduction-to-iis/introduction-to-iis-architecture)
- [AppCmd guide](https://learn.microsoft.com/en-us/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe)
- [IIS configuration reference](https://learn.microsoft.com/en-us/iis/configuration/)
- [ASP.NET Core hosting on IIS](https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/)
- [IIS request filtering](https://learn.microsoft.com/en-us/iis/configuration/system.webserver/security/requestfiltering/)

## Verification and scope

This guide contains original educational examples. Commands and configurations are illustrative and have not been executed against the learner's machine or an HTB target. Use only owned or explicitly authorized environments. Do not install services or change project deployments merely to complete Section 11. Do not expose lab services to the Internet. Avoid publishing full logs, configuration dumps, credentials or student data.
