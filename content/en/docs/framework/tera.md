---
title: "Tera"
description: "Cheatsheet on Tera"
lead: "Cheatsheet on Tera"
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

## Tera

[Tera](https://github.com/Keats/tera) is a template engine for Rust inspired by Jinja2 and the Django template language.

## Usage

Basic example:

```html
<title>{% block title %}{% endblock title %}</title>
<ul>
{% for user in users -%}
  <li><a href="{{ user.url }}">{{ user.username }}</a></li>
{%- endfor %}
</ul>
```

- Hot reloading: `tera.full_reload()?;`
- Loading templates from strings: `tera.add_raw_template("hello.html", "the body")?;`
- Render a one off template: `Tera::one_off(user_tpl, context, true);`

## Built-in

### Variables

- `__tera_context`: Magical variable is available in every template if you want to print the current context

```python
{{ __tera_context }}
```

### Filters

- [safe](https://tera.netlify.app/docs/#safe): Marks a variable as safe: HTML will not be escaped anymore.

```html
<h1>Hello {{ user.name | safe }} !</h1>
```

### Functions

- [get_env](https://tera.netlify.app/docs/#get-env): Returns the environment variable value for the name given.

```python
{{ get_env(name="SECRET") }} # Leak environment variables
```


## References

- [Tera Documentation](https://tera.netlify.app/docs/)
- [corCTF 2023 web/crabspace Writeup](https://www.cjxol.com/posts/corctf-2023-crabspace-web-writeup/)