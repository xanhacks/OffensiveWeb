---
title: "X-Content-Type-Options (XCTO)"
description: "Overview of X-Content-Type-Options (XCTO)"
lead: "Overview of X-Content-Type-Options (XCTO)"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "http"
weight: 620
toc: true
---

## X-Content-Type-Options (XCTO)

The [X-Content-Type-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options) response HTTP header is a marker used by the server to indicate that the MIME types advertised in the Content-Type headers should be followed and not be changed. The header allows you to avoid MIME type sniffing by saying that the MIME types are deliberately configured.

## Nosniff

`X-Content-Type-Options: nosniff` only apply request-blocking due to nosniff for request destinations of "script" and "style". However, it also enables Cross-Origin Read Blocking (CORB) protection for HTML, TXT, JSON and XML files (excluding SVG image/svg+xml).

You can find more details on the [CORB](/docs/http/cross-origin-read-blocking-corb/) page.