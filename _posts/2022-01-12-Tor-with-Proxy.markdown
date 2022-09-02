---
layout: post
title:  "Tor with Proxy"
date:   2022-01-12 19:45:41
categories: Tor proxy
---

# Tor
## Download and config Tor
https://www.torproject.org/
* `$ brew install tor`. Refer to https://2019.www.torproject.org/docs/tor-doc-osx.html.en
* `$ cp /usr/local/etc/tor/torrc.sample /usr/local/etc/tor/torrc`
* Run `tor` first before running crawl tasks.
* 如果是用 ShadowSocks Client 翻墙上网（往往需要），想让 Tor client 走 ShadowSocks Client 上网，需要向`/usr/local/etc/tor/torrc`中加一行：`Socks5Proxy 127.0.0.1:<PORT>`，其中的IP和<PORT>在 ShadowSocks 的`Advance Preferences`中可以看到。

## Run Tor
* `$ tor --RunAsDaemon 0 --CookieAuthentication 0 --HashedControlPassword "" --ControlPort 15001 --PidFile tor9001.pid --SocksPort 9001 --DataDirectory ~/tor/log/tor9001 --CircuitBuildTimeout 5 --KeepalivePeriod 60 --NewCircuitPeriod 15 --NumEntryGuards 8`
如果启动成功，会看到如下内容：
```markdown
...
Jan 12 17:56:19.000 [notice] Bootstrapped 0% (starting): Starting
...
Jan 12 17:56:23.000 [notice] Bootstrapped 15% (handshake_done): Handshake with a relay done
...
Jan 12 17:56:23.000 [notice] Bootstrapped 95% (circuit_create): Establishing a Tor circuit
Jan 12 17:56:24.000 [notice] Bootstrapped 100% (done): Done
```

## Check Tor
* https://check.torproject.org/ 可以用来测试目前是否是通过Tor来访问互联网。
`curl --socks5 localhost:9001 --socks5-hostname localhost:9001 -s https://check.torproject.org/ | cat | grep -m 1 Congratulations | xargs`
显示 `Congratulations. This browser is configured to use Tor.`

## Use Tor
### Use Tor in Chrome browser
* Use Chrome extension `Proxy SwithyOmega`.
* Set the proxy as `sock5` and `localhost`, port `9001`(tor default port is `9050`).

### Use Tor in code
```python
# These code is just a sample. Not correct.
proxy = 'socks5://localhost:9001'
driver=selenium.webdriver.Chrome
driver_options={'options': chrome_options_builder.get_options()}
driver(**driver_options)
chrome_options_builder = ChromeOptionsBuilder(device=device, proxy=proxy)
```

## torify
关于`torify`的以下内容仅供参考，可以跳过。`torify`是安装好Tor后自带一个command工具，帮助你查看走Tor访问URL时对应的IP。
Mac下需要先`$ brew install torsocks`，然后`$ cp /usr/bin/curl /usr/local/bin`，`$ torify /usr/local/bin/curl http://kong.getscroll.com/ip`。
不过即使做到这步，还是会报错，因为默认的Tor的`SocksPort`是`9050`，`torify`认准了`9050`端口了，即使我改`/usr/local/etc/tor/torrc`中的`SOCKSPort`也不会使`torify`去读，所以启动Tor时要用`tor --SocksPort 9050`才能用`torify`。
