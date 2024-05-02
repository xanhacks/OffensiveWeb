---
title: "HTMX"
description: "Cheatsheet on HTMX"
lead: "Cheatsheet on HTMX"
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

## HTMX

[htmx](https://htmx.org/) gives you access to AJAX, CSS Transitions, WebSockets and Server Sent Events directly in HTML, using attributes, so you can build modern user interfaces with the simplicity and power of hypertext.

## XSS

```html
<script src="https://unpkg.com/htmx.org@1.9.12"></script>

<img src=x data-hx-on:error="alert(1)" />
```