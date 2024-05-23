---
title: "Pug"
description: "Cheatsheet on Pug"
lead: "Cheatsheet on Pug"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "ssti"
weight: 620
toc: true
---

## Pug

[Pug](https://pugjs.org/) is a simple templating language that lets you generate HTML markup with plain JavaScript.

## XSS

### Unescaped Attributes

- [Unescaped Attributes - pugjs.org](https://pugjs.org/language/attributes.html#unescaped-attributes)
- [&attributes - pugjs.org](https://pugjs.org/language/attributes.html#attributes)

By default, all attributes are escaped. If you need to use special characters, use `!=` instead of `=`.

```html
div(escaped="<code>")
=> <div escaped="&lt;code&gt;"></div>
div(unescaped!="<code>")
=> <div unescaped="<code>"></div>

p = 'This code <strong>is</strong> escaped!'
=> <p>This code &lt;strong&gt;is&lt;/strong&gt; !</p>
p != 'This code is' + ' <strong>not</strong> escaped!'
=> This code is <strong>not</strong> escaped!

div#foo(data-bar="foo")&attributes({'data-foo': 'bar'})
=> <div id="foo" data-bar="foo" data-foo="bar"></div>
```

> Attributes applied using `&attributes` are not automatically escaped.

### Unescaped Strings

- [String Interpolation, Unescaped - pugjs.org](https://pugjs.org/language/interpolation.html#string-interpolation-unescaped)

**Safe:**

```bash
p You're logged in as #{user.name}
```

**Unsafe:**

```bash
p You're logged in as !{user.name}
```

### Unescaped Protocol

```js
a(href="javascript:alert(document.domain)")
```