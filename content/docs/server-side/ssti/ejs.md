---
title: "EJS"
description: "Cheatsheet on EJS"
lead: "Cheatsheet on EJS"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "ssti"
weight: 620
toc: true
---

## EJS - Embedded JavaScript templating

[EJS](https://ejs.co/) is a simple templating language that lets you generate HTML markup with plain JavaScript.

## XSS

**Unsafe:**

```html
<h1><%- user.name %></h1>
```

**Safe:**

```html
<h1><%= user.name %></h1>
```