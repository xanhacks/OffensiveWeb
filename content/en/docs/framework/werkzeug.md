---
title: "Werkzeug"
description: "Cheatsheet on Werkzeug"
lead: "Cheatsheet on Werkzeug"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "express"
weight: 620
toc: true
---

## Werkzeug

### Source code

- [werkzeug/debug/\_\_init\_\_.py#L179](https://github.com/pallets/werkzeug/blob/2.3.3/src/werkzeug/debug/__init__.py#L179): Debug PIN generation.
- [werkzeug/debug/\_\_init\_\_.py#L509](https://github.com/pallets/werkzeug/blob/2.3.3/src/werkzeug/debug/__init__.py#L509): Handle `?__debugger__=yes`.

### Debug mode

```python
probably_public_bits = [
  'root', # linux username
  'flask.app',
  'Flask',
  '/usr/local/lib/python3.11/site-packages/flask/app.py'
]

private_bits = [
    '157132472873131',
    # uuid.getnode()
    # /sys/class/net/eth0/address
    # int("8e:e9:41:3a:70:ab".replace(":", ""), 16)
    '6adbfbbe-c412-4d79-9110-2abace330a34'
    # /proc/sys/kernel/random/boot_id
]
```

- [HackTricks - Werkzeug / Flask Debug](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/werkzeug)