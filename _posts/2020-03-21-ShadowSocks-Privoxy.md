---
layout: post
title:  "ShadowSocks Privoxy"
date:   2020-03-21 09:52:11
categories: ShadowSocks
---

## Install ShadowSocks
* First, you need to make the server have the ability to visit some blocked sites like `google.com`.
```bash
# According to https://brickyang.github.io/2017/01/14/CentOS-7-%E5%AE%89%E8%A3%85-Shadowsocks-%E5%AE%A2%E6%88%B7%E7%AB%AF/
yum install python-setuptools && easy_install pip
pip install shadowsocks
vi /etc/shadowsocks.json
{
  "server": "your_ss_server_providor's_ip_or_domain",
  "server_port": <id which you must specify>,
  "password": "you must specify the password to connect to the server above",
  "local_address": "127.0.0.1",
  "local_port": 1080, # Must not be 0.
  "timeout": 300,
  "method": "aes-256-cfb", # There is no default method. So you should input it correctly!
  "workers": 1
}
nohup sslocal -c /etc/shadowsocks.json /dev/null 2>&1 &
```

## Privoxy
`export http_proxy=http://127.0.0.1:8118;export https_proxy=http://127.0.0.1:8118;`

* If you found the `Bad response. The server or forwarder response doesn't look like HTTP.` error, that's not the problem of Privoxy.
It's the problem of shadowsocks. You must make sure that the test:
`curl --socks5 127.0.0.1:1080 http://httpbin.org/ip` return an json including your IP.
`curl --socks5 127.0.0.1:1080 http://www.google.com`
