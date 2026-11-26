# Node.js — JavaScript HTTP Servers

Supplement to [Section 12](../12-Web-Servers.md). Node.js is a free, open-source cross-platform JavaScript runtime. It runs JavaScript outside a browser and provides built-in networking, HTTP, filesystem and stream APIs. Node.js is not Express, React, Vite, a database or a ready-configured general web server. Express is a separate framework; Vite is a frontend build/development tool.

## Architecture and concepts
V8 executes JavaScript; libuv supports the event loop and asynchronous I/O; the event loop schedules callbacks as work becomes ready. Promises represent future completion; async/await provides syntax for promise-based workflows. Streams process chunks rather than buffering all data. JavaScript callbacks generally run on an event-loop thread per isolate, while runtime operations may use other threads; CPU-heavy synchronous work can block requests. A module organizes code; npm manages packages; package.json describes dependencies and scripts; a lockfile records resolved versions.

## Original local server
Save as server.cjs in a disposable local directory; .cjs selects CommonJS regardless of package type.
```javascript
const http = require('node:http');

const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/health') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    return res.end(JSON.stringify({ status: 'ready' }));
  }
  res.writeHead(404, { 'Content-Type': 'application/json' });
  res.end(JSON.stringify({ error: 'Not found' }));
});

server.listen(3000, '127.0.0.1', () => {
  console.log('Local demo listening on port 3000');
});
```
require loads the built-in module; createServer registers a request handler; req.method and req.url describe the incoming message; writeHead sets status and headers; JSON.stringify serializes an object; end finishes the response; listen binds the server to the loopback address and port. This demonstration has no external dependencies. It deliberately does not implement authentication or persistent data.

```bash
node -v
node server.cjs
```
node -v shows the runtime version; node server.cjs runs the file. From another local terminal:
```bash
curl -i http://127.0.0.1:3000/health
curl -i http://127.0.0.1:3000/missing
```
Expected illustrative statuses: 200 and 404 respectively. Stop with Ctrl+C. On PowerShell use curl.exe. These are examples only, not observed output.

## Deployment and security
Use supported Node releases, lock dependencies and audit packages; avoid blocking event-loop work and unbounded body buffering; enforce content size, timeouts, schema validation, authorization and safe error handling; store secrets server-side; use TLS and trusted proxy configuration; avoid exposing debug/inspector ports; keep logs free of tokens and private data. For production, consider a reverse proxy such as NGINX, process supervision and a transactional database. Never trust client-side role checks.

ProTrack-MX uses Node tooling for React/Vite development, which does not imply its deployed backend runs a custom Node server. Supabase provides backend services. UniTrack uses Flask and does not need Node to replace Python; a future separate JavaScript API is optional. No project changed.

References: [Node.js introduction](https://nodejs.org/learn/getting-started/introduction-to-nodejs), [HTTP module](https://nodejs.org/api/http.html), [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices).
