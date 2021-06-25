---
layout: post
title:  "Tencent Cloud"
date:   2021-09-24 21:11:11
categories: ssl tencent cloud
---

# SSL 证书每年如何更新？
* 先重新申请一个证书，并下载证书。
* unzip the downloaded `xxx.zip` file, you can see a folder named `/Nginx`, and there are a `xxx.key` and a `xxx.crt` there.
* 可以参考 https://cloud.tencent.com/document/product/400/35244，如果是 CentOS 默认安装，可以参考如下：
```shell script
cat /etc/nginx/conf.d/your_conf_file
scp xxx.key root@your_server_ip:/etc/nginx/ssl
scp xxx.crt root@your_server_ip:/etc/nginx/ssl
nginx -s reload # Load the config changes.
```
