- Sits between clients and servers, and receives client requests and forwards them to appropriate backend servers, then returns the server's response to the client.
    - The client interacts only with the reverse proxy, unaware of the backend infrastructure.
- Primarily used for load balancing to distribute incoming requests across multiple backend servers to optimize resource utilization and improve performance.

```nginx
http {
    upstream backend_servers {
        server 192.168.1.10:8080;
        server 192.168.1.11:8080;
        server 192.168.1.12:8080;
    }

    server {
        listen 80;
        location / {
            proxy_pass http://backend_servers;
        }
    }
}
```

- Can cache static content, reducing the load on backend servers and improving response times.

```nginx
# Caching
http {
    proxy_cache_path /path/to/cache levels=1:2 keys_zone=my_cache:10m;

    server {
        listen 80;
        location / {
            proxy_cache my_cache;
            proxy_pass http://backend_server;
        }
    }
}
```

- Can be used to rewrite URLs before forwarding requests to backend servers.

```nginx
# URL Rewriting
server {
    listen 80;
    location /api/ {
        rewrite ^/api/(.*)$ /$1 break;
        proxy_pass http://api_server;
    }
}
```

- Can be used to enhance security by hiding backend server details and implementing web application firewalls.

```nginx
server {
    listen 80;
    server_tokens off;

    location / {
        proxy_pass http://backend_server;
        proxy_set_header Server "Custom Server Name";
    }
}
```

- Can centralize logging and monitoring for multiple backend servers.

```nginx
http {
    log_format detailed '$remote_addr - $remote_user [$time_local] '
                        '"$request" $status $body_bytes_sent '
                        '"$http_referer" "$http_user_agent"';

    server {
        listen 80;
        access_log /var/log/nginx/access.log detailed;

        location / {
            proxy_pass http://backend_server;
        }
    }
}
```
