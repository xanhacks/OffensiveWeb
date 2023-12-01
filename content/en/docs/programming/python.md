---
title: "Python"
description: "Overview of the Python programming language"
lead: "Overview of the Python programming language"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "programming"
weight: 620
toc: true
---

## Python

[Python](https://www.python.org/) is a high-level, interpreted programming language known for its simplicity and readability.

## Modules

### os.path

```python
os.path.join("uploads/", "flag.txt")  # uploads/flag.txt
os.path.join("uploads/", "/flag.txt") # /flag.txt
```

### re

```python
>>> re.match("\.", "../")
<re.Match object; span=(0, 1), match='.'>
>>> re.match("\.", "/../") # no match!
```