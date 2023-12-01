---
title: "Same Origin Method Execution"
description: "Cheatsheet on Same-Origin-Method-Execution (SOME)"
lead: "Cheatsheet on Same-Origin-Method-Execution (SOME)"
date: 2020-10-06T08:49:31+00:00
lastmod: 2020-10-06T08:49:31+00:00
draft: false
images: []
menu:
  docs:
    parent: "client-side"
weight: 630
toc: true
---

## Definition

**Same-Origin Method Execution (SOME)** is a security vulnerability that allows an attacker to execute functions of a web application in the context of another user's session. This attack exploits the same-origin policy of web browsers but requires that the target website contains a suitable vulnerable function and that the attacker tricks the user into executing malicious JavaScript in their browser.

## References

- [Real World CTF 2023 - The cult of 8 bit](https://www.ctfiot.com/90966.html)
- [Youtube - Intigriti XSS challenge 1022](https://www.youtube.com/watch?v=EZfPrgrV5p4)
- [HackTricks - SOME](https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting/some-same-origin-method-execution)
- [SOME Attack](https://www.someattack.com/Playground/About)
- [Bypass CSP Using WordPress By Abusing Same Origin Method Execution](https://octagon.net/blog/2022/05/29/bypass-csp-using-wordpress-by-abusing-same-origin-method-execution/)