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
    parent: "python"
weight: 600
toc: true
---

## Python

[Python](https://www.python.org/) is a high-level, interpreted programming language known for its simplicity and readability.

## Modules

### os.path

```python
os.path.join("uploads/", "flag.txt")  # uploads/flag.txt
os.path.join("uploads/", "/flag.txt") # /flag.txt
os.path.join("uploads/", "../flag.txt")  # uploads/../flag.txt
```

### re

| Function | Description |
|:---------:|:-----------|
|[re.match()](https://docs.python.org/3/library/re.html#re.match)|checks for a match only at the beginning of the string |
| [re.search()](https://docs.python.org/3/library/re.html#re.search) | checks for a match anywhere in the string |
| [re.fullmatch()](https://docs.python.org/3/library/re.html#re.fullmatch) | checks for entire string to be a match |

```python
>>> re.match(r"\.", "../../../../etc/passwd") # match!
<re.Match object; span=(0, 1), match='.'>

>>> re.match(r"\.", "/../../../../etc/passwd") # no match!
```

Testing Python regex:

- [Regex Tester Online](https://pythex.org/)
- [Visual Regex Tester Online](https://pythonium.net/regex)
