# NGINX reverse proxy

NGINX Open Source can accept HTTP requests and forward to upstream applications. Routing, header handling, buffering, TLS and load balancing are configurable. A reverse proxy represents backend servers. [Official downloads](https://nginx.org/en/download.html), [reverse proxy guide](https://docs.nginx.com/nginx/admin-guide/web-server/reverse-proxy), [proxy module](https://nginx.org/en/docs/http/ngx_http_proxy_module.html).

Ubuntu isolated VM: sudo apt update; sudo apt install nginx (may start service). For Windows/Docker use supported official instructions; Linux native configuration below assumes same namespace. Example for owned local Flask development app:
```nginx
server {
    listen 127.0.0.1:8081;
    server_name localhost;
    location / {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;
    }
}
```
listen restricts local access, location matches path, proxy_pass identifies backend, proxy_set_header controls forwarded metadata. Edge overwrites untrusted forwarding headers; multiple proxies need explicit trust chain. sudo nginx -t validates config; nginx -v displays version. Only on an approved isolated instance, after validation and review, reload via sudo nginx -s reload. curl -i http://127.0.0.1:8081/ includes response headers if running. Docker container loopback differs from host. Do not expose Flask debug server to internet. Troubleshoot 502 upstream errors, 504 timeouts, TLS hostname and config errors. Production needs trusted TLS, origin restrictions, limits, logs, patches, application auth. UniTrack is a natural local example; ProTrack-MX managed backend may not permit arbitrary proxy topology. No config deployed or tested.
