---
title: "JSONP"
description: "Overview of JSONP."
lead: "Overview of JSONP."
date: 2020-10-06T08:49:31+00:00
lastmod: 2020-10-06T08:49:31+00:00
draft: false
images: []
menu:
  docs:
    parent: "client-side"
weight: 630
toc: true
---

## Definition

JSONP (`JSON with padding`) is a technique used in web development to enable cross-origin data sharing between two domains. It exists primarily to bypass the same-origin policy imposed by web browsers, which restricts JavaScript from reading JSON cross-site.

The same-origin policy is a security measure that prevents one website from interfering with another. However, sometimes it's necessary for a website to access data or resources from another domain, such as when using APIs provided by other services. JSONP provides a way to achieve this by exploiting the fact that `<script>` tags in HTML are not subject to the same-origin policy.

Here's how JSONP works:

1. Instead of making a regular AJAX request, the client includes a `<script>` tag in their HTML, pointing to the URL of the server providing the data.
2. The server responds with a JavaScript file containing a JSON object wrapped in a function call. The name of the function is specified by the client as a query parameter in the URL.
3. When the client receives the response, it executes the JavaScript code, which calls the specified function with the JSON data as an argument.
4. The client can then process the JSON data as needed.

JSONP exists because it provides a simple and effective way to bypass the same-origin policy and enable cross-origin data sharing between domains. Modern web development techniques such as CORS (`Cross-Origin Resource Sharing`) provide more secure and flexible ways to achieve cross-origin data sharing.

## CSP Bypass

JSONP can help an attacker to bypass `Content Security Policy`'s `script-src` directive if a domain with an exposed JSONP endpoint is included in the policy's value.

## JSONP Endpoint List

- [Github - zigoo0/JSONBee](https://github.com/zigoo0/JSONBee/blob/master/jsonp.txt)