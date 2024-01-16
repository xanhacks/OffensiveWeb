---
title : "Burpsuite"
description: "Offensive Web - Documentation on the tool Burpsuite"
lead: "Offensive Web - Documentation on the tool Burpsuite"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "others"
weight: 620
toc: true
---

## BCheck

BChecks are custom scan checks that you can create and import. Burp Scanner runs these checks in addition to its built-in scanning routine, helping you to target your scans and make your testing workflow as efficient as possible. Source [portswigger.net](https://portswigger.net/burp/documentation/scanner/bchecks).

- Add a new BCheck: `Extensions` > `BChecks` > `Import`

You can debug your BChecks by running unit tests on specified requests. To add a request, right-click on it and then click `Send to BChecks editor`.

### Examples

File: `open_redirect.bcheck`

```
metadata:
  language: v2-beta
  name: "Open Redirect"
  description: "Looks for Open Redirects inside GET parameters."
  author: "xanhacks"
  tags: "openredirect", "redirection"


run for each:
	parameter =
		"redirect_url",
		"redirect",
		"callback",
		"callback_url"

given request then
	if `?{parameter}=http` in {base.request.url} or `?{parameter}=/` in {base.request.url} or `?{parameter}=javascript` in {base.request.url} then
		report issue:
			severity: info
			confidence: tentative
			detail: `Open Redirect found at {base.request.url}`
			remediation: "Make sure the URL is validated."
	end if
```