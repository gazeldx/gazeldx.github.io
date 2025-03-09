---
layout: post
title:  "Why Ruby on Rails is better than Python Django?"
date:   2024-05-15 17:23:19
categories: Rails Django
---

# Why Ruby on Rails is better than Python Django for agile web development?

* Rails released earlier than Django. So Django learned a lot from Rails.

* For all the files under `app` folder (Rails' application folder), you don't need to use `require` to load application code.

This is called [autoloading](https://guides.rubyonrails.org/getting_started.html#autoloading). 

If you added external gems to `Gemfile`, you also don't need to `require ` to use them. They are automatically loaded.

But in Django, you have to write code like:

```python
import requests
from .models import Profile
```

This is not as convenient as Rails. This matters since when you are using console like `rails console` or `python manage.py shell -i ipython` to debug, 

for Rails users, they can write your debugging code like `Article.all` directly. 

But Django user has to write `from myapp.models import Article` before running `Article.objects.all()`.  

So do importing third-party package.

* Ruby has `ERB` which allow you to write any Ruby code in a document. The bellow code is a mixture of HTML and ERB. ERB is a templating system that evaluates Ruby code embedded in a document.

```html
<h1>Articles</h1>

<ul>
  <% @articles.each do |article| %>
    <li>
      <%= article.title %>
    </li>
  <% end %>
</ul>
```

But Django doesn't allow you to write Python code in their template. 
Google [can i use python object and call method in django template](https://www.google.com/search?q=can+i+use+python+object+and+call+method+in+django+template&oq=can+I+use+python+object+and+call+method+in+&gs_lcrp=EgZjaHJvbWUqBwgBECEYoAEyBggAEEUYOTIHCAEQIRigATIHCAIQIRigATIHCAMQIRigATIHCAQQIRigATIHCAUQIRigATIKCAYQIRgWGB0YHjIHCAcQIRiPAjIHCAgQIRiPAtIBCTI0MTI4ajBqN6gCALACAA&sourceid=chrome&ie=UTF-8) you will know it.
[Django template](https://docs.djangoproject.com/en/5.0/topics/templates/)
