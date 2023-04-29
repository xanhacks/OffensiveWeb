---
title: "Express.js"
description: "Cheatsheet on Express.js library"
lead: "Cheatsheet on Express.js library"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "topics"
weight: 620
toc: true
---

## Express stack

[Express](https://www.npmjs.com/package/express) is a minimalist web framework for Node.js which has 31 [dependencies](https://www.npmjs.com/package/express?activeTab=dependencies).

{{< details "List of dependencies" >}}
- [accepts](https://www.npmjs.com/package/accepts): Return a different typed respond body based on what the client wants to accept.
- [array-flatten](https://www.npmjs.com/package/array-flatten): Flatten nested arrays (e.g. `flatten([1, [2, 3]])` => `[1, 2, 3]`.
- [body-parser](https://www.npmjs.com/package/body-parser): HTTP request body parsing middleware (`req.body` property).
- [content-disposition](https://www.npmjs.com/package/content-disposition): Create and parse HTTP `Content-Disposition` header.
- [content-type](https://www.npmjs.com/package/content-type): Create and parse HTTP `Content-Type` header.
- [cookie](https://www.npmjs.com/package/cookie): Basic HTTP cookie parser and serializer.
- [cookie-signature](https://www.npmjs.com/package/cookie-signature): Sign and unsign cookies.
- [debug](https://www.npmjs.com/package/debug): JavaScript debugging utility (`debug(...)`, `DEBUG='*' node index.js`).
- [depd](https://www.npmjs.com/package/depd): Mark things deprecated.
- [encodeurl](https://www.npmjs.com/package/encodeurl): Encode a URL to a percent-encoded form, excluding already-encoded sequences.
- [escape-html](https://www.npmjs.com/package/escape-html): Escape string for use in HTML (HTML entity, e.g. `&` => `&amp;`).
- [etag](https://www.npmjs.com/package/etag): Generates HTTP ETags in HTTP responses.
- [finalhandler](https://www.npmjs.com/package/finalhandler): Function to invoke as the final step to respond to HTTP request.
- [fresh](https://www.npmjs.com/package/fresh): Check for caching using `if-none-match` and `etag` HTTP headers.
- [http-errors](https://www.npmjs.com/package/http-errors): Create HTTP errors.
- [merge-descriptors](https://www.npmjs.com/package/merge-descriptors): Merge objects using descriptors (call descriptors and merge).
- [methods](https://www.npmjs.com/package/methods):
- [on-finished](https://www.npmjs.com/package/on-finished):
- [parseurl](https://www.npmjs.com/package/parseurl):
- [path-to-regexp](https://www.npmjs.com/package/path-to-regexp):
- [proxy-addr](https://www.npmjs.com/package/proxy-addr):
- [qs](https://www.npmjs.com/package/qs):
- [range-parser](https://www.npmjs.com/package/range-parser):
- [safe-buffer](https://www.npmjs.com/package/safe-buffer):
- [send](https://www.npmjs.com/package/send):
- [serve-static](https://www.npmjs.com/package/serve-static):
- [setprototypeof](https://www.npmjs.com/package/setprototypeof):
- [statuses](https://www.npmjs.com/package/statuses):
- [type-is](https://www.npmjs.com/package/type-is):
- [utils-merge](https://www.npmjs.com/package/utils-merge):
- [vary](https://www.npmjs.com/package/vary):
{{< /details >}}

## Query string parsing

| URL                       | Content of request.query.foo in code                |
| ------------------------- | --------------------------------------------------- |
| `?foo=bar`                | `'bar'` (string)                                    |
| `?foo=bar&foo=baz`        | `['bar', 'baz']` (array of string)                  |
| `?foo[]=bar`              | `['bar']` (array of string)                         |
| `?foo[]=bar&foo[]=baz`    | `['bar', 'baz']` (array of string)                  |
| `?foo[bar]=baz`           | `{ bar : 'baz' }` (object with a key)               |
| `?foo[]=bar`              | `['bar']` (array of string)                         |
| `?foo[]baz=bar`           | `['bar']` (array of string - postfix is lost)       |
| `?foo[][baz]=bar`         | `[ { baz: 'bar' } ]` (array of object)              |
| `?foo[bar][baz]=bar`      | `{ foo: { bar: { baz: 'bar' } } }` (object tree)    |
| `?foo[10]=bar&foo[9]=baz` | `[ 'baz', 'bar' ]` (array of string - notice order) |
| `?foo[toString]=bar`      | `{}` (object where calling `toString()` will fail)  |

> Table from [OWASP - Node.js Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Nodejs_Security_Cheat_Sheet.html#perform-input-validation).

## Trust proxy

Enabling `trust proxy` will have the following impact:

- `req.hostname` = `X-Forwarded-Host`
- `req.protocol` = `X-Forwarded-Proto` (https or http or even an invalid name)
- `req.ip` and `req.ips` = `X-Forwarded-For`

The trust proxy setting is implemented using the [proxy-addr](https://www.npmjs.com/package/proxy-addr) package. See [docs](https://expressjs.com/en/guide/behind-proxies.html).
