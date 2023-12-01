---
title: "SSRF - Server-side Request Forgery"
description: "Solutions to common problems."
lead: "Solutions to common problems."
date: 2020-11-12T15:22:20+01:00
lastmod: 2020-11-12T15:22:20+01:00
draft: false
images: []
menu:
  docs:
    parent: "server-side"
weight: 620
toc: true
---

## Definition

**Server-Side Request Forgery (SSRF)** enables an attacker to induce a server to make requests to internal or external resources, potentially accessing or manipulating data and systems that are not directly accessible to the attacker.

## URI Scheme

**URI Scheme Lists:** gopher, tftp, http, https, ldap, ftp, dict, ssh2, file, ogg, expect, imap, smtp, telnet, ...

- [tftp](https://en.wikipedia.org/wiki/Trivial_File_Transfer_Protocol): TFTP is a simple protocol for transferring files, implemented on top of the UDP/IP protocols (default port: 69)
- [dict](https://en.wikipedia.org/wiki/DICT): DICT is a dictionary network protocol, TCP client/server (default port: 2628)

From [PHP Wrappers](https://www.php.net/manual/en/wrappers.php):
- file:// — Accessing local filesystem
- http:// — Accessing HTTP(s) URLs
- ftp:// — Accessing FTP(s) URLs
- php:// — Accessing various I/O streams
- zlib:// — Compression Streams
- data:// — Data (RFC 2397)
- glob:// — Find pathnames matching pattern
- phar:// — PHP Archive
- ssh2:// — Secure Shell 2
- rar:// — RAR
- ogg:// — Audio streams
- expect:// — Process Interaction Streams

### Gopher protocol

Overview:
- [Gopher URI](https://datatracker.ietf.org/doc/html/rfc4266): `gopher://<host>:<port>/<gopher-path>`
- Example: `gopher://attacker.com:1234/_Hello%0AWorld`
- Default port: 70

SSRF to RCE using [Gopherus](https://spyclub.tech/2018/08/14/2018-08-14-blog-on-gopherus/):
- [tarunkant/Gopherus](https://github.com/tarunkant/Gopherus): Generates Gopher payload for exploiting SSRF and gaining RCE, on SSRF vulnerable sites. Supports: MySQL, FastCGI, Memcached, Redis, Zabbix, SMTP.

## Bypass

### TOCTOU

- [Time-of-check to time-of-use](https://en.wikipedia.org/wiki/Time-of-check_to_time-of-use)
- Create a domain that resolve a whitelisted IP address, then the target IP address once the check is bypassed.

### Redirection

```php
<?php header("gopher://localhost:11211/1stats%0aquit"); ?>
```

## Blind SSRF

### Reponse delay scanning

To detect if a port or a host is available, you can send a request to it a hundred times and then calculate the average response time. A small difference in response time between an available or unavailable resource can be detected.

Example:

- 100 requests to `10.10.100.2:8080` divided by 100 = 50ms
- 100 requests to `10.10.100.2:1337` divided by 100 = 200ms

## DoS

- Load big file (ex: 10GB)
- Abuse long timeout

