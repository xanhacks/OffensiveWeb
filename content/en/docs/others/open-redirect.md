---
title: "Open Redirect"
description: "Overview of Open Redirect"
lead: "Overview of Open Redirect"
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

## Definition

**Open Redirect** (or OPR) occurs when a web application redirects a user to an URL without properly validating the redirect target.

## OPR to XSS

When you have a client-side redirection using `window.location = target`, you can trigger an XSS attack using the `javascript:` protocol. Example:

```js
window.location = "javascript:alert(document.domain)"
// ?redirect=javascript:alert(document.domain)
```

## OPR to External URLs

**Open Redirect to external URLs** can enhance the success rate of phishing attacks if victims trust the main domain, but it still requires user interaction from the victim.

Example: `https://example.com?redirect=https://evil.com`

## Execution After Redirect (EAR)

**Execution After Redirect** happens when a server redirects a user but executes code on the server before the redirection occurs. Example:

```php
<?php
session_start();

if (!isset($_SESSION['auth'])) {
	header("Location: /login");
}

echo "Private content!";
```

You can access the private content in the response body if the execution is not terminated after the redirection.

```
$ curl -v 'http://localhost:8888'
< HTTP/1.1 302 Found
< Host: localhost:8888
< Location: /login
< [...]

Private content!
```

Fix using the `exit` function in PHP:

```php
if (!isset($_SESSION['auth'])) {
	header("Location: /login");
	exit();
}
```

## Follow redirects

**Follow redirects** refers to the process where an HTTP client automatically proceeds the redirection. Examples:

```python
>>> import requests
>>> requests.get("http://localhost:8888?redirect=https://xanhacks.xyz").url
'https://xanhacks.xyz'
```

```js
fetch("/?redirect=https://xanhacks.xyz")
.then(r => r.text())
.then(html => console.log(html)) // HTML content of xanhacks.xyz
```

## OPR on famous websites

### Google

Open Redirect is not considered a vulnerability by Google, so they will not fix the following ones:

```
https://www.youtube.com/attribution_link?u=https://m.youtube.com@xanhacks.xyz
https://www.youtube.com/attribution_link?u=https://m.youtube.com.xanhacks.xyz
https://www.youtube.com/logout?continue=https://google.com/amp/xanhacks.xyz
https://accounts.google.com/ServiceLogin?continue=https://google.com/amp/xanhacks.xyz
https://accounts.google.com/SignOutOptions?hl=fr&continue=https://google.com/amp/xanhacks.xyz
https://google.com/amp/xanhacks.xyz
```