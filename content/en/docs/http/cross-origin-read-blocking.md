---
title: "Cross-Origin Read Blocking"
description: "Overview of Cross-Origin Read Blocking"
lead: "Overview of Cross-Origin Read Blocking"
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

## Cross-Origin Read Blocking (CORB)

[Cross-Origin Read Blocking](https://chromium.googlesource.com/chromium/src/+/master/services/network/cross_origin_read_blocking_explainer.md) (CORB) is a security feature designed to mitigate the risk of certain types of cross-origin information leaks and attacks.

This is a measure beyond what is enforced by the Same-Origin Policy (SOP) and Cross-Origin Resource Sharing (CORS).

CORB mitigates the following attack vectors:

1. **Cross-Site Script Inclusion (XSSI)**: XSSI is the technique of pointing the `<script>` tag at a target resource which is not JavaScript, and observing some side effects when the resulting resource is interpreted as JavaScript. CORB prevents this class of attacks, because a CORB-protected resource will be blocked from ever being delivered to a cross-site `<script>` element.
2. **Speculative Side Channel Attack (e.g. Spectre)**: An attacker may use an `<img src="https://example.com/secret.json">` element to pull a cross-site secret into the process where the attacker's JavaScript runs, and then use a speculative side channel attack (e.g. Spectre) to read the secret. CORB can prevent this class of attacks when used in tandem with [Site Isolation](https://www.chromium.org/Home/chromium-security/site-isolation/) (ensures that pages from different websites are always put into different processes, each running in a sandbox that limits what the process is allowed to do), by preventing the JSON resource from being present in the memory of a process hosting a cross-site page.

### How CORB Works

CORB works by examining the MIME type of cross-origin responses and blocking those that should not be made available to the requesting web page due to their sensitive nature.

### Key Aspects of CORB

- **MIME Type Evaluation**: CORB checks the MIME type of a cross-origin response against a list of "protected" MIME types (such as HTML, XML (excluding SVG), and JSON). If the MIME type of the response is on this list, CORB may block the response from being consumed by the requesting site.
- **Blocking Strategy**: If CORB decides to block a response, it does so by stripping the response body, effectively preventing the requesting JavaScript from reading the content. However, the request itself is not blocked, the server still receives the request and sends a response. The difference is that the JavaScript on the requesting site cannot access the response's body.
- **Context of the Request**: CORB's decision to block a response also considers the context in which the request was made. For example, a script element trying to load JSON data might be blocked, as scripts are not supposed to load such data types directly.
- **Interaction with Other Security Features**: Although CORB operates independently, its effectiveness is complemented by other security measures such as the `X-Content-Type-Options: nosniff` header, which prevents the browser from sniffing the MIME type of a response. This ensures that incorrectly labeled resources are not executed in an unintended context.