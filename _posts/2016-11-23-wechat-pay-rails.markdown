---
layout: post
title:  "微信支付"
date:   2016-11-23 14:40:24
categories: Rails Wechat
---

微信支付退款时, 需要设置好证书, 否则会报错: RestClient::ServerBrokeConnection 。
设置证书的方法: 先去https://pay.weixin.qq.com/ 的 'API安全'中 下载证书。然后把证书上传到服务器上, 之后修改
initialize/wx_pay.rb

{% highlight ruby %}
WxPay.set_apiclient_by_pkcs12(File.read("/path/to/cert/apiclient_cert.p12"), WxPay.mch_id)
{% endhighlight %}

