---
layout: post
title:  "微信支付"
date:   2016-11-23 14:40:24
categories: Rails Wechat
---

### 微信支付

微信支付退款时, 需要设置好证书, 否则会报错: RestClient::ServerBrokeConnection 。

设置证书的方法: 先去 https://pay.weixin.qq.com/ 的 'API安全'中 下载证书。

然后把证书上传到服务器上, 之后修改`initialize/wx_pay.rb`

```ruby
WxPay.set_apiclient_by_pkcs12(File.read("/path/to/cert/apiclient_cert.p12"), WxPay.mch_id)
```

### 微信其它

You can customize the share link title and etc when user open your link from wechat.

http://mp.weixin.qq.com/wiki/7/aaa137b55fb2e0456bf8dd9148dd613f.html#.E8.8E.B7.E5.8F.96.E2.80.9C.E5.88.86.E4.BA.AB.E5.88.B0.E6.9C.8B.E5.8F.8B.E5.9C.88.E2.80.9D.E6.8C.89.E9.92.AE.E7.82.B9.E5.87.BB.E7.8A.B6.E6.80.81.E5.8F.8A.E8.87.AA.E5.AE.9A.E4.B9.89.E5.88.86.E4.BA.AB.E5.86.85.E5.AE.B9.E6.8E.A5.E5.8F.A3

