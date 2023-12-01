---
title: "Cookie"
description: "Cheatsheet on Cookies"
lead: "Cheatsheet on Cookies"
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

## Cookies

[Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies) are small pieces of data stored on a user's device by a web browser, used to remember information about the user, such as login details, preferences, browsing activity...

## Cookies Attributes

### HttpOnly

The `HttpOnly` attribute for cookies ensures they cannot be accessed or manipulated by client-side scripts, offering protection against threats like XSS attacks.

### Secure

The `Secure` attribute ensures cookies are sent only over HTTPS, preventing potential interception or tampering during transmission.

### HostOnly

The `HostOnly` attribute for cookies ensures they are accessible only to the domain that set them, excluding its subdomains.

### Domain

The `Domain` attribute specifies which hosts can receive the cookie. If a cookie from `example.com` sets its `Domain` attribute to `.example.com` (notice the leading dot), the cookie will be accessible to both `example.com` and its subdomains, like `sub.example.com`.

### SameSite

The `SameSite` attribute controls when cookies are sent to the server based on the origin of the request, which helps to reduce certain types of cross-site request vulnerabilities.

1. **Strict**: Only be sent in a request if it's made from the same site as the cookie's origin.
2. **Lax**: The cookie will be sent in a top-level navigation request (e.g., following a link) but not in requests initiated by third-party websites.
3. **None**: This explicitly states that the cookie is intended to be accessed across sites.

> More at [MDN - SameSite](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#samesitesamesite-value) and [Article - SameSite confusion](https://jub0bs.com/posts/2021-01-29-great-samesite-confusion/).

## Cookie Ordering

- Arranged alphabetically based on their names
- Sorted by their paths in alphabetical sequence (root path `/` is first)

## Set-cookie from Javascript

### Chrome

| Origin domain   | Working           | Blocked           |
|:---------------:|:-----------------:|:-----------------:|
| example.com     | example.com       |                   |
|                 | .example.com      |                   |
|                 | sub.example.com   |                   |
| sub.example.com | example.com       |                   |
|                 | .example.com      |                   |
|                 | sub.example.com   |                   |
|                 | sub2.example.com  |                   |

### Firefox

| Origin domain   | Working          | Blocked           |
|:---------------:|:----------------:|:-----------------:|
| example.com     | example.com      | sub.example.com   |
|                 | .example.com     |                   |
| sub.example.com | example.com      | sub2.example.com  |
|                 | .example.com     |                   |
|                 | sub.example.com  |                   |

## Lax bypass

```html
window.open("https://example.com") // Lax cookies are send
<iframe src="..."></iframe>        // Lax cookies are NOT send
```