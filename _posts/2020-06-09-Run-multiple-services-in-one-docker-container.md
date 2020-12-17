---
layout: post
title:  "Run multiple services in one docker container"
date:   2020-06-09 21:11:11
categories: docker
---

## Notice
* You should read the previous article `2020-06-08 Mac-Django-uwsgi-Nginx` first.

Sometimes you have the need to use only one container but run all the services which are implemented 
 by docker-compose.

How can we do it without using `docker-compose` but with only one `Dockerfile`?

We need to use install all these services in one image and use `Supervised` to start all the services.

The example, we have a django project started by uWSGI and a Nginx.

Please see my previous article to see how to make them work on your local machine first.

Then the second step will be the docker part. 

## Dockerfile
```dockerfile
FROM tiangolo/uwsgi-nginx:python3.8

ENV PYTHONUNBUFFERED 1

RUN rm -rf /app/*

COPY ./config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY ./config/mysite_nginx.conf /etc/nginx/conf.d/mysite_nginx.conf
COPY ./config/uwsgi.ini /app/uwsgi.ini

RUN groupadd -r django \
    && useradd -r -g django django

COPY ./api/requirements.frozen /requirements.frozen
RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r /requirements.frozen \
    && rm /requirements.frozen

COPY ./compose/production/django/uwsgi.sh /uwsgi.sh
RUN sed -i 's/\r//' /uwsgi.sh
RUN chmod +x /uwsgi.sh
RUN chown django /uwsgi.sh

COPY ./api /app

WORKDIR /app

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]

ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8000
```

## supervisord
```bash
pip install supervisor
supervisord -n
```

* `touch supervisord.conf`
```ini
[supervisord]
nodaemon=true

[program:uwsgi]
command=/uwsgi.sh
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stdout
stderr_logfile_maxbytes=0

[program:nginx]
command=/usr/sbin/nginx
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0
```

## Docker
* In Docker container, if you want to connect to Database in Host, you can use:
`host.docker.internal`

* `docker run --publish 80:8000 --env-file .env --detach --name container5 built2`

* `touch ./compose/production/django/uwsgi.sh`
```shell script
#!/usr/bin/env bash

set -o errexit
set -o pipefail
set -o nounset

cd /app

python manage.py collectstatic --noinput

python manage.py migrate

/usr/local/bin/uwsgi --ini /app/uwsgi.ini --die-on-term
```

* The image even don't have `vi` command. So sometimes you need to replace words in a file by `sed -i 's/8000/80/' mysite_nginx.conf `
