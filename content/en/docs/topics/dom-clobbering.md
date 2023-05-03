---
title: "DOM Clobbering"
description: "Overview of DOM Clobbering"
lead: "Overview of DOM Clobbering"
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

## Getting started

DOM clobbering is a technique in which you inject HTML into a page to manipulate the DOM.

It is particularly useful in cases where XSS is not possible, but you can control some HTML on a page where the attributes `id` or `name` are whitelisted by the HTML filter. The most common form of DOM clobbering uses an anchor element to overwrite a global variable.

> Reference [PortSwigger - DOM clobbering](https://portswigger.net/web-security/dom-based/dom-clobbering).

### Attribute id

Example with a random tag with a `id` attribute:

```html
<h1 id="hd">Super title !</h1>

<script>
console.log(hd);            // <h1 id="hd">Super title !</h1>
console.log(window.hd);     // <h1 id="hd">Super title !</h1>
console.log(document.hd);   // undefined
console.log(hd.toString()); // [object HTMLHeadingElement]
console.log(hd.innerText);  // Super title !
</script>
```

### Attribute name

Example with a `form` tag with a `name` attribute:

```html
<form name="fm" method="GET" action="/login"></form>

<script>
console.log(fm);          // <form name="fm"></form>
console.log(window.fm);   // <form name="fm"></form>
console.log(document.fm); // <form name="fm"></form>
console.log(fm.method);   // get
console.log(fm.action);   // http://localhost/login
</script>
```

List of tags which supports the name attribute:
- `embed`, `form`, `iframe`, `image`, `img`, `object`

{{< alert icon="👉" >}}
**document.fm** (name=fm) is defined but NOT **document.hd** (id=hd).
{{< /alert >}}

{{< details "Element name fuzzing - Proof of Concept" >}}
```js
<body><script>
const tags = ["a", "abbr", "acronym", "address", "applet", "area", "article", "aside", "audio", "b", "base", "bdi", "bdo", "bgsound", "big", "blink", "blockquote", "body", "br", "button", "canvas", "caption", "center", "cite", "code", "col", "colgroup", "data", "datalist", "dd", "del", "details", "dfn", "dialog", "dir", "div", "dl", "dt", "em", "embed", "fieldset", "figcaption", "figure", "font", "footer", "form", "frame", "frameset", "h1", "head", "header", "hgroup", "hr", "html", "i", "iframe", "image", "img", "input", "ins", "kbd", "keygen", "label", "legend", "li", "link", "main", "map", "mark", "marquee", "menu", "menuitem", "meta", "meter", "nav", "nobr", "noembed", "noframes", "noscript", "object", "ol", "optgroup", "option", "output", "p", "param", "picture", "plaintext", "portal", "pre", "progress", "q", "rb", "rp", "rt", "rtc", "ruby", "s", "samp", "script", "section", "select", "slot", "small", "source", "spacer", "span", "strike", "strong", "style", "sub", "summary", "sup", "table", "tbody", "td", "template", "textarea", "tfoot", "th", "thead", "time", "title", "tr", "track", "tt", "u", "ul", "var", "video", "wbr", "xmp"];

let valids = [];
tags.forEach(tag => {
	document.body.innerHTML = `<${tag} name="xanhacks"></${tag}>`;
	try {
		if (xanhacks !== undefined)
			valids.push(tag);
	} catch (e) {}
});
console.log(valids); // ['embed', 'form', 'iframe', 'image', 'img', 'object']
</script></body>
```
{{< /details >}}

## Other attributes

Unfortunately, you can only use `id` or `name`.

{{< details "Fuzzing other attributes - Proof of Concept" >}}
```html
<body><script>
const tags = ["a", "abbr", "acronym", "address", "applet", "area", "article", "aside", "audio", "b", "base", "bdi", "bdo", "bgsound", "big", "blink", "blockquote", "body", "br", "button", "canvas", "caption", "center", "cite", "code", "col", "colgroup", "data", "datalist", "dd", "del", "details", "dfn", "dialog", "dir", "div", "dl", "dt", "em", "embed", "fieldset", "figcaption", "figure", "font", "footer", "form", "frame", "frameset", "h1", "head", "header", "hgroup", "hr", "html", "i", "iframe", "image", "img", "input", "ins", "kbd", "keygen", "label", "legend", "li", "link", "main", "map", "mark", "marquee", "menu", "menuitem", "meta", "meter", "nav", "nobr", "noembed", "noframes", "noscript", "object", "ol", "optgroup", "option", "output", "p", "param", "picture", "plaintext", "portal", "pre", "progress", "q", "rb", "rp", "rt", "rtc", "ruby", "s", "samp", "script", "section", "select", "slot", "small", "source", "spacer", "span", "strike", "strong", "style", "sub", "summary", "sup", "table", "tbody", "td", "template", "textarea", "tfoot", "th", "thead", "time", "title", "tr", "track", "tt", "u", "ul", "var", "video", "wbr", "xmp"];
const attributes = ["accept", "accept-charset", "accesskey", "action", "align", "allow", "alt", "async", "autocapitalize", "autocomplete", "autofocus", "autoplay", "background", "bgcolor", "border", "buffered", "capture", "challenge", "charset", "checked", "cite", "class", "code", "codebase", "color", "cols", "colspan", "content", "contenteditable", "contextmenu", "controls", "coords", "crossorigin", "csp", "data", "data-*", "datetime", "decoding", "default", "defer", "dir", "dirname", "disabled", "download", "draggable", "enctype", "enterkeyhint", "for", "form", "formaction", "formenctype", "formmethod", "formnovalidate", "formtarget", "headers", "height", "hidden", "high", "href", "hreflang", "http-equiv", "id", "integrity", "intrinsicsize", "inputmode", "ismap", "itemprop", "keytype", "kind", "label", "lang", "language", "loading", "list", "loop", "low", "manifest", "max", "maxlength", "minlength", "media", "method", "min", "multiple", "muted", "name", "novalidate", "open", "optimum", "pattern", "ping", "placeholder", "playsinline", "poster", "preload", "readonly", "referrerpolicy", "rel", "required", "reversed", "role", "rows", "rowspan", "sandbox", "scope", "scoped", "selected", "shape", "size", "sizes", "slot", "span", "spellcheck", "src", "srcdoc", "srclang", "srcset", "start", "step", "style", "summary", "tabindex", "target", "title", "translate", "type", "usemap", "value", "width", "wrap"];

let valids = [];
tags.forEach(tag => {
	attributes.forEach(attribute => {
		if (attribute != "id" && attribute != "name") {
			document.body.innerHTML = `<${tag} ${attribute}="xanhacks"></${tag}>`;
			try {
				if (xanhacks !== undefined)
					valids.push([tag, attribute]);
			} catch (e) {}
		}
	});
});
console.log(valids); // [] - Empty array
</script></body>
```
{{< /details >}}

## Value of variable

```html
<a id="link" href="xyzxyz" lang="fr" class="blue"></a>

<script>
console.log(link);            // <a id="link" href="xyzxyz"></a>
console.log(link.toString()); // http://localhost/xyzxyz
console.log(link.href);       // http://localhost/xyzxyz
console.log(link.value);      // undefined
console.log(link.lang);       // fr
console.log(link.class);      // undefined
</script>
```

You can retrieve some attributes like `href` and `lang` but not `class`. You can list all the attributes you can retrieve on a specific element by doing fuzzing.

{{< details "Attribute enumeration - Proof of Concept" >}}
```html
<body><script>
const tags = ["a", "abbr", "acronym", "address", "applet", "area", "article", "aside", "audio", "b", "base", "bdi", "bdo", "bgsound", "big", "blink", "blockquote", "body", "br", "button", "canvas", "caption", "center", "cite", "code", "col", "colgroup", "data", "datalist", "dd", "del", "details", "dfn", "dialog", "dir", "div", "dl", "dt", "em", "embed", "fieldset", "figcaption", "figure", "font", "footer", "form", "frame", "frameset", "h1", "head", "header", "hgroup", "hr", "html", "i", "iframe", "image", "img", "input", "ins", "kbd", "keygen", "label", "legend", "li", "link", "main", "map", "mark", "marquee", "menu", "menuitem", "meta", "meter", "nav", "nobr", "noembed", "noframes", "noscript", "object", "ol", "optgroup", "option", "output", "p", "param", "picture", "plaintext", "portal", "pre", "progress", "q", "rb", "rp", "rt", "rtc", "ruby", "s", "samp", "script", "section", "select", "slot", "small", "source", "spacer", "span", "strike", "strong", "style", "sub", "summary", "sup", "table", "tbody", "td", "template", "textarea", "tfoot", "th", "thead", "time", "title", "tr", "track", "tt", "u", "ul", "var", "video", "wbr", "xmp"];
const attributes = ["accept", "accept-charset", "accesskey", "action", "align", "allow", "alt", "async", "autocapitalize", "autocomplete", "autofocus", "autoplay", "background", "bgcolor", "border", "buffered", "capture", "challenge", "charset", "checked", "cite", "class", "code", "codebase", "color", "cols", "colspan", "content", "contenteditable", "contextmenu", "controls", "coords", "crossorigin", "csp", "data", "data-*", "datetime", "decoding", "default", "defer", "dir", "dirname", "disabled", "download", "draggable", "enctype", "enterkeyhint", "for", "form", "formaction", "formenctype", "formmethod", "formnovalidate", "formtarget", "headers", "height", "hidden", "high", "href", "hreflang", "http-equiv", "id", "integrity", "intrinsicsize", "inputmode", "ismap", "itemprop", "keytype", "kind", "label", "lang", "language", "loading", "list", "loop", "low", "manifest", "max", "maxlength", "minlength", "media", "method", "min", "multiple", "muted", "name", "novalidate", "open", "optimum", "pattern", "ping", "placeholder", "playsinline", "poster", "preload", "readonly", "referrerpolicy", "rel", "required", "reversed", "role", "rows", "rowspan", "sandbox", "scope", "scoped", "selected", "shape", "size", "sizes", "slot", "span", "spellcheck", "src", "srcdoc", "srclang", "srcset", "start", "step", "style", "summary", "tabindex", "target", "title", "translate", "type", "usemap", "value", "width", "wrap"];

let valids = [];
tags.forEach(tag => {
	attributes.forEach(attribute => {
		if (attribute != "id" && attribute != "name") {
			document.body.innerHTML = `<${tag} id="xyz" ${attribute}="xanhacks"></${tag}>`;
			try {
				if (eval(`xyz.${attribute} == "xanhacks"`))
					valids.push([tag, attribute]);
			} catch (e) {}
		}
	});
});
console.log(valids); // [...] - a lot
</script></body>
```
{{< /details >}}

