---
layout: post
title:  "Study from Django official website"
date:   2025-10-10 11:34:24
categories: Python Django
---

### Django 5 new features:
* Form field group: The concept of a field group was added to the templates' system to simplify form field rendering.

`as_field_group()` renders fields with the `"django/forms/field.html"`, `<div class="col">{{ form.email.as_field_group }}</div>`.

* `django.contrib.auth.middleware.LoginRequiredMiddleware` Redirects all unauthenticated requests to a login page.

### Django 5
```shell
python manage.py runserver 9000
python -m django --version

mkdir djangotutorial
django-admin startproject mysite djangotutorial # This will create a project called mysite inside the djangotutorial directory
```

structure:
```
djangotutorial/
    manage.py # A command-line utility
    mysite/
        __init__.py
        settings.py
        urls.py 
        asgi.py
        wsgi.py # An entry-point for WSGI-compatible web servers
```

```shell
python manage.py startapp polls

python manage.py migrate mysite 0339
```

```
polls/
    __init__.py
    admin.py        # 
    apps.py
    migrations/     #
        __init__.py
    models.py       #
    tests.py
    urls.py         #
    views.py        #
```

* `views.py`:
```python
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```


What Is the OSI Model
The Open Systems Interconnection (OSI) model describes seven layers that computer systems use to communicate over a network.
