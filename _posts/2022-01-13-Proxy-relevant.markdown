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

## Clash
### Clash pro
Clash pro has limited feature. I prefer to use `Clash for windows`.   
For x86 Intel CPU of Macbook,
* Click `Config - Remote config - Manage -Add`. For `Url`, input a valid url, then click the `OK` button.
* If the Url is valid, you should see that there would be a list of proxy there if you click `Dashboard`.
* Then click `Set as system proxy` (you can `Wifi - Network preferences - Advanced - Proxies`) to 
check `Web Proxy, Secure Web Proxy, SOCKS Proxy)`.

### Clash for windows
* Visit [clash_for_windows_pkg/releases](https://github.com/Fndroid/clash_for_windows_pkg/releases),
for x86 Intel CPU of Macbook, you can download the [Clash.for.Windows-0.20.3.dmg](https://github.com/Fndroid/clash_for_windows_pkg/releases/download/0.20.3/Clash.for.Windows-0.20.3.dmg)
* Click `Profiles`, enter the URL and click `Download`.
* Click `General`, set the `Port` as `7890`. Then click `System Proxy` to enable it.
Now you can try to open a web browser and test if you can visit google.com to verify your proxy.
You should be able to visit google.com, but your browser started via puppeteer may still can not visit google.com. Then it is time to enable `TUN Mode`.
* Click `General`, then click `TUN Mode` to enable it.
Then verify your browser started via puppeteer again. You should be able to visit google.com.

* References: https://www.youtube.com/watch?v=h4Js3gBRzwU

About [TUN](https://en.wikipedia.org/wiki/TUN/TAP).

This solution can make which shadowSocks could not make because shadowSocks doesn't support `TUN mode`.

#### If a proxy could not be used, it may not the issue of the proxy. It may because the network (like WIFI) which is not able to visit google.com. Use Clash's `TUN Mode` can solve it.
