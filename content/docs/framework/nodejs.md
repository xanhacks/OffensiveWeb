---
title: "Node.js"
description: "Cheatsheet on Node.js"
lead: "Cheatsheet on Node.js"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "framework"
weight: 620
toc: true
---

## Node.js

[Node.js](https://github.com/nodejs/node) is an open-source, cross-platform JavaScript runtime environment.

## Tips

### Data import

- [nodejs.org - data: imports](https://nodejs.org/api/esm.html#data-imports)

data: URLs are supported for importing with the following MIME types:

- text/javascript for ES modules
- application/json for JSON
- application/wasm for Wasm


```js
import 'data:text/javascript,console.log("hello!");';
import _ from 'data:application/json,"world!"' assert { type: 'json' }; 
```