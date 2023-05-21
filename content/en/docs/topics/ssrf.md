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
    parent: "help"
weight: 620
toc: true
---

## Blind SSRF

### Reponse delay scanning

To detect if a port or a host is available, you can send a request to it a hundred times and then calculate the average response time. A small difference in response time between an available or unavailable resource can be detected.

Example:

- 100 requests to `10.10.100.2:8080` divided by 100 = 50ms
- 100 requests to `10.10.100.2:1337` divided by 100 = 200ms

