---
layout: post
title:  "Proxy relevant"
date:   2022-01-12 19:45:41
categories: Proxy
---

[What is browser fingerprinting](https://smartproxy.com/blog/what-is-browser-fingerprinting)

## Network errors
* Run `curl -x "socks5://198.1.1.1:9999" "https://www.google.com/"` got error: `curl: (97) SOCKS5 reply has wrong version, version should be 5.`
This is a network issue. You can switch to another WIFI connection.

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
