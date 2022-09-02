---
layout: post
title:  "How to build SwitchyOmega"
date:   2022-01-12 19:45:41
categories: Build SwitchyOmega
---

There are some issues when you try to build switchyOmega since this repo maintainer seems to be not have much time on solving issues there.

First, I went to https://github.com/FelisCatus/SwitchyOmega/releases and clicked
https://github.com/FelisCatus/SwitchyOmega/archive/refs/tags/v2.5.20.zip

Then I read the `README.md` and see the `## Building the project` there. I read it and when I run `npm run deps`, I got this error:
```
bower angular-spectrum-colorpicker#~1.3.5      ECMDERR Failed to execute "git ls-remote --tags --heads https://github.com/Jimdo/angular-spectrum-colorpicker.git", exit code of #128 fatal: unable to access 'https://github.com/Jimdo/angular-spectrum-colorpicker.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443

Additional error details:
fatal: unable to access 'https://github.com/Jimdo/angular-spectrum-colorpicker.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
```

To fix it, just `vim omega-web/bower.json`, change the line `angular-spectrum-colorpicker` to
`"angular-spectrum-colorpicker": "https://github.com/screeps/angular-spectrum-colorpicker.git"`.

Other issues are network issues. Just change your proxy of command line console would solve them.

Run:
```shell
pip3 install requests
pip3 install PySocks
python3
```

then run:
```python
import requests
socks5 = "socks5://198.1.1.1:9999"
proxy = dict(http=socks5, https=socks5)
res = requests.get('https://google.com', timeout=5, proxies=proxy)
res.content
```
Got error: 
```text
MaxRetryError: SOCKSHTTPSConnectionPool(host='google.com', port=443): Max retries exceeded with url: / (Caused by NewConnectionError('<urllib3.contrib.socks.SOCKSHTTPSConnection object at 0x108a61810>: Failed to establish a new connection: SOCKS5 proxy server sent invalid data'))
```
This is also a network issue (The same as the `curl` example. You can switch to another WIFI connection.)
