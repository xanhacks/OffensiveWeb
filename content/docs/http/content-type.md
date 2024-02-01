---
title: "Content-Type"
description: "Overview of Content-Type"
lead: "Overview of Content-Type"
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

## Content-Type

[Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type) is an HTTP header used to indicate the media type (MIME type) of the resource being sent in the response body, such as "text/html" for HTML documents or "application/json" for JSON data.

## Script loading Content-Type page

What happens if a `script` tag load a javascript file from a page that returns `Content-Type: font/ttf`.

**Result**:

| Disabled    | Allowed           |
| ----------- | ----------------- |
| video/*     | application/*     |
| image/*     | font/*            |
| audio/*     | message/*         |
|             | multipart/*       |
|             | text/*            |
|             | `empty` or `random value`  |

So, it works with:
- CSV, PDF, XML, HTML, JSON...
- Invalid, valid: `Content-Type: image/png, video/something, text/html`

---

You can get the full list of MIME types on [www.iana.org](https://www.iana.org/assignments/media-types/media-types.xhtml). To extract all the MIME types from the website, you can use the following javascript snippet:

```js
let types = [];
Array.from(document.getElementsByTagName("a")).forEach(elem => {
  if (elem.getAttribute("href") === elem.innerText && elem.innerText.split("/").length == 2)
		types.push(elem.innerText);
})
console.log(types);
```

Then, open your web console and Right click -> Copy Object.


---

**script.php**

```php
<?php

$type = $_GET["content_type"];
header("Content-Type: $type");

?>
loaded = true;
```

**index.php**

```html
<head></head>

<script>
const data = [
  "application/1d-interleaved-parityfec",
  "application/3gpdash-qoe-report+xml",
  "...",
  "video/vnd.vivo",
  "video/VP8",
  "video/VP9"
];

function rec(min, max) {
	if (min > data.length || min >= max) {
		console.log(found);
		return;
	}
	let item = data[min];
	// console.log("Try with : " + item);

	loaded = false;
	let head = document.getElementsByTagName("head")[0];
	let script = document.createElement("script");
	script.src = `/script.php?content_type=${item}`;
	script.id = item;

	script.addEventListener("load", function() {
		if (loaded) found.push(item);
		script.remove();
		return rec(min + 1, max);
	});
	script.addEventListener("error", function() {
		script.remove();
		return rec(min + 1, max);
	});

	head.appendChild(script);
};

var loaded = false;
var found = [];
async function main() {
	for (let i = 0; i < data.length; i += 100) {
		rec(i, i + 100);
		await new Promise(r => setTimeout(r, 3000));
	}
	console.log("done!");
};
main();
</script>
```
