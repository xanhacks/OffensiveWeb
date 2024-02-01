---
title: "Strange behavior"
description: "Strange behavior of Javascript"
lead: "Strange behavior of Javascript"
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

## Uppercase

Certain Unicode characters, when uppercased, actually expand in length.

```js
for (let i = 0; i < 0x10FFFF; i++) {
	var c = String.fromCodePoint(i);
	var upper = c.toUpperCase();
	if (c.length !== upper.length)
		console.log(i, c, c.length, upper, upper.length);
}
```

```
223 ß 1 SS 2
329 ŉ 1 ʼN 2
496 ǰ 1 J̌ 2
912 ΐ 1 Ϊ́ 3
944 ΰ 1 Ϋ́ 3
1415 և 1 ԵՒ 2
7830 ẖ 1 H̱ 2
7831 ẗ 1 T̈ 2
...
```