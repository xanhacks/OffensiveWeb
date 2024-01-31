---
title: "Cross-Origin Resource Policy (CORP)"
description: "Overview of Cross-Origin Resource Policy (CORP)"
lead: "Overview of Cross-Origin Resource Policy (CORP)"
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

## Cross-Origin Resource Policy (CORP)

[Cross-Origin Resource Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cross-Origin_Resource_Policy) is a policy set by the `Cross-Origin-Resource-Policy` HTTP header that lets websites and applications opt in to protection against certain requests from other origins (such as those issued with elements like `<script>` and `<img>`), to mitigate speculative side-channel attacks, like Spectre, as well as Cross-Site Script Inclusion attacks.

CORP is an additional layer of protection beyond the default same-origin policy. Cross-Origin Resource Policy complements Cross-Origin Read Blocking (CORB), which is a mechanism to prevent some cross-origin reads by default.

> As this policy is expressed via a response header, the actual request is not prevented. Rather, the browser prevents the result from being leaked by stripping the response body.

## Usage

- `Cross-Origin-Resource-Policy: same-site`: Only requests from the same Site can read the resource.
- `Cross-Origin-Resource-Policy: same-origin`: Only requests from the same origin (i.e. scheme + host + port) can read the resource.
- `Cross-Origin-Resource-Policy: cross-origin`: Requests from any origin (both same-site and cross-site) can read the resource.

## Example

In the provided example, the script `profile.js` will only load if both the client and server originate from the same site. For instance, if the client's origin is `app.example.com`, the script will load successfully. However, if the client comes from a different site, such as `app.anothersite.com`, the script will not load.

Client source code:

```html
<script src="https://cdn.example.com/user/profile.js"></script>
<script>
	setTimeout(() => {
		console.log(secret);
	}, 500);
</script>
```

Server (e.g. CDN) source code:

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "const secret = 1337;" 

@app.after_request
def add_response_headers(response):
    response.headers['Cross-Origin-Resource-Policy'] = 'same-site'
    return response

if __name__ == '__main__':
    app.run(debug=True)
```