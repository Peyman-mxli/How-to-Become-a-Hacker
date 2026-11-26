# Apache Tomcat — Java Web Application Hosting

Supplement to [Section 12](../12-Web-Servers.md). Apache Tomcat is a free, open-source Java servlet container under the Apache License 2.0. It implements Jakarta Servlet and related web specifications and can receive HTTP requests itself or sit behind Apache HTTP Server or NGINX. Tomcat and Apache HTTP Server are different projects.

## Architecture and terminology
- JVM: Java Virtual Machine, executes Java bytecode.
- Servlet: Java component handling requests and responses.
- Jakarta Servlet: specification defining the servlet API; older Java EE applications use javax.servlet rather than jakarta.servlet.
- JSP/Jakarta Pages: technology for generating dynamic pages on the server.
- WAR: Web Application Archive, a deployable Java web application package.
- Connector: receives network traffic such as HTTP.
- Catalina: servlet container; Coyote: connector implementation; Jasper: JSP engine.
- Context: deployed web application, typically associated with a URL prefix.
- CATALINA_HOME: installation directory; CATALINA_BASE: instance-specific configuration and runtime directories.

A typical path: browser -> HTTP connector -> servlet container -> servlet/application logic -> database. Tomcat is not a database or a complete implementation of every Jakarta EE feature.

## Configuration and files
Typical instance directories: conf/server.xml (connectors and server settings), conf/web.xml (default web settings), webapps/ (deployment), logs/ (logs), and bin/ (launch scripts in the installation). Default ports vary with configuration; 8080 is a common HTTP connector port. Review installed documentation and Java compatibility before deploying.

Original local connector illustration inside a suitable Service element in server.xml:
```xml
<Connector address="127.0.0.1" port="8080"
           protocol="HTTP/1.1" connectionTimeout="20000" />
```
address binds loopback; port selects the socket; protocol selects the HTTP connector; connectionTimeout is in milliseconds. Do not add duplicate connectors. Restart according to the instance's service manager after validating config in a disposable environment.

## Original servlet example
For a compatible Jakarta Servlet project with the Servlet API dependency provided by Tomcat:
```java
import java.io.IOException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@WebServlet("/health")
public class HealthServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response) throws IOException {
        response.setContentType("text/plain;charset=UTF-8");
        response.getWriter().println("School service ready");
    }
}
```
WebServlet maps the route; HttpServlet defines HTTP handlers; doGet handles GET; setContentType declares text encoding; getWriter writes response content. Build a WAR using a compatible Java build tool, deploy it to an owned Tomcat instance and use its context path. For example, a WAR named school.war commonly maps to /school; expected illustrative GET result is HTTP 200 and text School service ready.

```bash
java -version
curl -i http://127.0.0.1:8080/school/health
```
java -version displays JVM version; curl -i includes response headers and body. No installations or execution claimed.

## Security and project applications
Patch Java and Tomcat; remove or restrict unused management applications; restrict connector and administrative access; run with least privilege; keep secrets outside deployment artifacts; validate uploads; disable unnecessary connectors; configure TLS and trusted reverse proxies; avoid verbose errors. Tomcat could host a future Java school API, but neither Flask UniTrack nor React/Supabase ProTrack-MX requires it. It can serve as an alternative architecture study.

References: [Tomcat documentation](https://tomcat.apache.org/), [version compatibility](https://tomcat.apache.org/whichversion.html), [Jakarta Servlet](https://jakarta.ee/specifications/servlet/). All examples are illustrative, local-only and not required HTB exercises.
