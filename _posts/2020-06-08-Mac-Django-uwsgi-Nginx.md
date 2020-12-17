---
layout: post
title:  "Mac-Django-uwsgi-Nginx"
date:   2020-06-08 21:11:11
categories: uwsgi nginx
---

https://www.sean-lan.com/2016/09/15/django-uwsgi-nginx/

https://uwsgi-docs.readthedocs.io/en/latest/tutorials/Django_and_nginx.html

### nginx
`less /usr/local/etc/nginx/nginx.conf`, you can see `include servers/*;`.
```
cd /usr/local/etc/nginx
mkdir servers
```

* `cd /path/to/mysite`

### mysite_nginx.conf
* `touch config/mysite_nginx.conf`
```
upstream django {
    server unix:///tmp/mysite.sock;
}

server {
    listen      8000;
    server_name localhost;
    charset     utf-8;

    client_max_body_size 6M; # max upload size

    location /media  {
        alias /path/to/mysite/media;
    }

    location /static {
        alias /path/to/mysite/static;
    }

    location / {
        uwsgi_pass  django;
        include     /usr/local/etc/nginx/uwsgi_params;
    }
}
```

### static files 
```
mkdir static
python manage.py collectstatic
```

```python
ROOT_DIR = '/path/to/mysite'
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(ROOT_DIR, "static/")

MEDIA_ROOT = os.path.join(ROOT_DIR, 'media')
MEDIA_URL = '/media/'
```

### uwsgi.ini
* `touch config/uwsgi.ini`
```ini
[uwsgi]
chdir = /path/to/mysite
module = mysite.wsgi:application
socket = /tmp/mysite.sock
chown-socket = nginx:nginx
chmod-socket = 664
cheaper = 1
processes = 2
master = true
vacuum=True
pidfile = /tmp/mysite.pid
daemonize = uwsgi.log
```

### Start or stop
#### uWSGI
* `uwsgi --ini ./config/uwsgi.ini`.

stop:
```
kill -INT `cat /tmp/mysite.pid`
uwsgi --stop /tmp/mysite.pid
```

#### Nginx
```bash
nginx -s stop
nginx
```
