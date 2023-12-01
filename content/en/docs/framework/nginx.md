---
title: "Nginx"
description: "Cheatsheet on Nginx"
lead: "Cheatsheet on Nginx"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "framework"
weight: 620
toc: true
---

## Nginx

[Nginx](https://nginx.org/) is a high-performance web server, reverse proxy, and load balancer, known for its efficiency and low resource consumption.

## Passing Request Headers

By default, NGINX eliminates the header fields whose values are empty strings and redefines two header fields in proxied requests:

- `Host` is set to the `$proxy_host` variable
- `Connection` is set to `close`

To change these setting, as well as modify other header fields, use the `proxy_set_header` directive.

## Caching

By default, NGINX Plus caches all responses to requests:

- HTTP methods: `GET` and `HEAD`
- Cache key: Request string

 Reponses are cached the first time received from a proxied server. If a request has the same key as a cached response, NGINX Plus sends the cached response to the client. [Learn more](https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/)

 ```
 http {
    # ...
    proxy_cache_path /data/nginx/cache keys_zone=mycache:10m;
    server {
        proxy_cache mycache;
        location / {
            proxy_pass http://localhost:8000;
        }
    }
}
```

To change the request characteristics used in calculating the key, include the `proxy_cache_key` directive:

```
proxy_cache_key "$host$request_uri$cookie_user";
```

## Security

### Basic Auth

- `auth_basic_user_file`: The path to the .htpasswd file that contain user/password pairs
- Package `apache2-utils` or `httpd-tools`
- Command `htpasswd -c /etc/apache2/.htpasswd user1`

### Filtering

- `allow` / `deny`: Allow or deny access from particular IP addresses / subnets

### Rate limit

Limit of 1 request per minute based on IP address (binary mode for performance). [Learn more](https://docs.nginx.com/nginx/admin-guide/security-controls/controlling-access-proxied-http/#limiting-the-request-rate)

```
http {
    #...
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
    server {
        limit_req zone=one;
        # ...
    }
}
```

For requests that arrive at the full bucket, NGINX will respond with the `503 Service Unavailable` error (if not redefined with `limit_req_status`).
