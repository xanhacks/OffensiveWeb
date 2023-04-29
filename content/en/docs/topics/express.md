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
