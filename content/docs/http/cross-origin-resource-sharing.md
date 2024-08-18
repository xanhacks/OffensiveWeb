---
title: "Cross-Origin Resource Sharing (CORS)"
description: "Overview of Cross-Origin Resource Sharing (CORS)"
lead: "Overview of Cross-Origin Resource Sharing (CORS)"
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

## Cross-Origin Resource Sharing (CORS)

Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources. CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request. In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request.

For security reasons, browsers restrict cross-origin HTTP requests initiated from scripts. For example, fetch() and XMLHttpRequest follow the same-origin policy. This means that a web application using those APIs can only request resources from the same origin the application was loaded from unless the response from other origins includes the right CORS headers.

## CORS scope

### In Scope

- `fetch()` or `XMLHttpRequest`
- Web Fonts (`@font-face` within CSS)
- WebGL textures
- Images/video frames drawn to a canvas using `drawImage()`
- CSS Shapes from images

### Out of Scope

- Form submission
- Script, image, iframe & object tags (e.g. `<script src=...>` or `<object data=...>`)
- ...
