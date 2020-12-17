---
layout: post
title:  "JIRA-Atlassian-Connect-App-Django"
date:   2020-11-17 21:11:11
categories: Rails
---

# Django
For Atlassian connect app, we would better use Django instead of Django rest framework.

Our first step is to build a local Django app server as a Atlassian connect app and install it. 

## Django app server as a Atlassian connect app
So please create a Django app (it was named as `jira_app` for this example) first.

* `base_settings.py`
```python
ROOT_DIR = environ.Path(__file__) - 3

TEMPLATES = [
    {
        'DIRS': [os.path.join(ROOT_DIR, 'jira_app', 'templates')],
    }
]

ATLASSIAN_CONNECT_BASE_URL = env('ATLASSIAN_CONNECT_BASE_URL')
```

* `mysite/urls.py`
```python
urlpatterns = [
    path('jira/', include('jira_app.urls')),
]
```

* `jira_app/views.py`
```python
from django.conf import settings
from django.http import JsonResponse
from django.shortcuts import render
from django.views.decorators.clickjacking import xframe_options_exempt


def atlassian_connect(request):
    base_url = settings.ATLASSIAN_CONNECT_BASE_URL
    name = f"my jira test {None if settings.ENVIRONMENT == 'prod' else ' ' + settings.ENVIRONMENT}"

    return JsonResponse({
        'baseUrl': base_url,
        'key': f'my jira test-app-{settings.ENVIRONMENT}',
        'authentication': {
            'type': 'none'
        },
        'name': name,
        'description': f'my jira test {settings.ENVIRONMENT}',
        'links': {
            'self': f'{base_url}/jira/atlassian-connect.json'
        },
        'scopes': [
            'read',
            'write'
        ],
        'apiVersion': 1,
        'modules': {
            'generalPages': [
                {
                    'url': '/jira/hello.html',
                    'key': 'hello',
                    'location': 'system.top.navigation.bar',
                    'name': {
                        'value': 'hello'
                    }
                }
            ]
        }
    })


@xframe_options_exempt
def hello(request):
    return render(request, 'jira_app/hello.html')
```

* `jira_app/urls.py`
```python
from django.urls import path

from . import views

urlpatterns = [
    path('atlassian-connect.json', views.atlassian_connect, name='atlassian-connect'),
    path('hello.html', views.hello, name='hello'),
]
```

* `jira_app/templates/jira_app/hello.html`
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <link rel="stylesheet" href="https://unpkg.com/@atlaskit/css-reset@2.0.0/dist/bundle.css" media="all">
    <script src="https://connect-cdn.atl-paas.net/all.js" async></script>
  </head>

  <body>
    <section id="content" class="ac-content">
      <h1>Hello</h1>
    </section>
  </body>
</html>

```

## Install this app in Jira
Visit https://your-project.atlassian.net/secure/BrowseProjects.jspa

Then click `Apps -> Manage your apps`.

Then click `upload app` (should click `Settings -> Enable development mode` first).

Then input `From this URL` with the Ngrok url of your app like `https://544119900028.ngrok.io/jira/atlassian-connect.json`.

# JWT
## Get the install json and sharedSecret
```python
import json
from django.http import HttpResponse
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def installed(request):
    print('======== request.body: ', request.body)
    body = json.loads(request.body)
    sharedSecret = body['sharedSecret']
    print('======== sharedSecret: ', sharedSecret)
    return HttpResponse("You posted to /jira/installed.")
```

This is what I get from `request.body` when installing an app:
```json
{
  "key": "some-app",
  "clientKey": "d388d673-a684-3f88-a3e1-7646d7a27058",
  "publicKey": "MIGfMA0GCSq4SIb3DQEBAQUAA4GNADCBiQKBgQC4MfoLPnyiKXPeGR+PD+Qj1Puyzi/jRInJShzfu89mQddLpoIS8SjAmzETCfH86MtHUI4BmP7qgUEIXuHggSyd6Okpi1X4v/W+zYjCzrl5uWql88qbKksHhoovbjcZtVCE/4zUSJ7s8smyW7Xc80KhAFlhqbesbwOQhbvQdLk9uQIDAQAf",
  "sharedSecret": "WkvYYm5PawgaOgt6hFacPOTFZrRlk/8pZWmZi8TgnW9irP4HRVuACocB6b7+rlKTemgyJlZP3sdCd5RTlSDrdg",
  "serverVersion": "100150",
  "pluginsVersion": "1001.0.0.SNAPSHOT",
  "baseUrl": "https://some-domain.atlassian.net",
  "productType": "jira",
  "description": "Atlassian JIRA at https://team-1605254797493.atlassian.net ",
  "eventType": "installed"
}
```

## Other
* I guess an connect app can be installed in many clients (different from the `subdomain` name).

For example Like:

https://some-subdomain.atlassian.net/secure/BrowseProjects.jspa?selectedProjectType=software

https://other-people-subdomain.atlassian.net/secure/BrowseProjects.jspa?selectedProjectType=software

The `install callback` will be invoked when a `some-subdomain.atlassian.net` admin installs your app.

https://developer.atlassian.com/cloud/jira/platform/security-for-connect-apps/#signed-installation-callback-requests

So the `sharedSecret` of all the users under the same `some-subdomain.atlassian.net` will be always the same.

Use https://github.com/jpadilla/pyjwt to decode.

* Once user uninstalled an app and reinstall again, in the `install` callback, the `Authorization` will be in the `headers` and
the `sharedSecret` will still be the old one to ensure that our Python code could decode it.

But by `body = json.loads(request.body); sharedSecret = body['sharedSecret']`, you can get a new `sharedSecret`.
And the old `sharedSecret` which you have just used will become stale in the future.

So you have to update the `sharedSecret` for this client.

* According to https://blog.developer.atlassian.com/understanding-jwt/ , the `clientKey` distinguished the 
client (e.g: 
`your-subdomain.atlassian.net` has clientKey `d388d675-a684-3f88-a3e1-7646d8227059`
`his-subdomain.atlassian.net`  has clientKey `932fb277-c58c-3b4b-aae4-03634313c7bf`
)

* At this moment, I think you may want to save the `clientKey` and the `sharedSecret` into database.

Then, when a request arrives, you can decode it with `sharedSecret`. But which `sharedSecret` should you use?

The tip is that in every request, there is a parameter `xdm_e': 'https://thegreatworld.atlassian.net'` which is the same as the `baseUrl` in `installation request json`
we can use to determine which `clientKey` is because we will save records in databases like:
```log
your-subdomain.atlassian.net d388d675-a684-3f88-a3e1-7646d8227059 sharedSecret1345
his-subdomain.atlassian.net  932fb277-c58c-3b4b-aae4-03634313c7bf sharedSecret2824
...
```

https://pypi.org/project/atlassian-jwt/
