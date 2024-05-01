---
title: "Server-Side Caching"
description: "Overview of Server-Side Caching"
lead: "Overview of Server-Side Caching"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "server-side"
weight: 620
toc: true
---

## Definition

Server-side web caching is a technique used to improve website performance by storing frequently accessed data on the server. The server checks if the requested data is already stored in its cache before generating a new response, which can significantly reduce response time and improve user experience.

Caching is based on multiple parameters like URL paths, MIME types, file extensions, HTTP methods, and headers, which help determine which resources are cacheable and for how long.

## Cache Poisoing

Cache poisoning is aimed at manipulating the client-side cache to force clients to load resources that are unexpected, partial, or under the control of an attacker. For example, you can elevate a self-XSS to a stored XSS if you manage to store the XSS inside the cache.

## Web Cache Deception

The goal of Web Cache Deception is to trick victims into loading resources that will be cached with sensitive information.

Examples:
- Abusing wildcard using Path Traversal: `https://chat.openai.com/share/%2F..%2Fapi/auth/session`
- Abusing cached file extension: `https://chat.openai.com/api/auth/session/test.css`

## Softwares

### Cloudflare

- [Cloudflare Docs - Default Cache Behavior](https://developers.cloudflare.com/cache/concepts/default-cache-behavior/)

### Varnish

- [Varnish Docs - Introduction](https://docs.varnish-software.com/varnish-enterprise/)
- [Getting Started with Varnish Cache - Linode](https://www.linode.com/docs/guides/getting-started-with-varnish-cache/)