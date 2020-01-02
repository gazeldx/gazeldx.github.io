---
layout: post
title:  "Android Push"
date:   2019-11-15 09:52:11
categories: Android
---

This article maybe outdated.

# Conclusion
* 不用第三方推送，完全自建，集成华米OV四大厂商的推送，其它小厂商才用第三方通道。
* 手机客户端可以通过源代码控制走哪个通道的（根据ROM判断厂商），由此不用担心给同一个用户走了两个通道，导致发再次相同消息的问题。举例：小米用户，只会走小米的那段安卓推送源代码，其它厂商或第三方平台的的推送源代码根本就没有被执行到。
* 在服务端devices表中维护一个topics数组字段，这个字段用于和上述5（4+1）个通道的同步（这里的topics(小米华为称之为topics)和tags(OPPO VIVO称之为tags)含义相同，其实就是设备订阅的topic的意思，见sub,pub模式解释）。
* 四大平台厂商的接口，我只用两个。1，更新topics 2，按topics推送消息。

# My Views
* 华米OV四大厂商各自的推送，仅限于自己品牌的手机走系统级推送（所以我们把他们通通集成一遍，以保证到达率）。
* 第三方推送平台付费版本做的，就是根据手机所属厂商分别走四大平台的系统级推送通道（1、在客户端提供的SDK包是一个支持四平台的大包 2、在服务端对接四个平台的API）
* 作为App开发商，我们自己可以把第三方推送平台所实现的这些事自己做了（网上主流说法是自建代价太大，请问他自已自建过没有？其实不见得。）。
* 作为App服务端开发人员，需要Android客户端开发人员告诉设备的厂商，然后区分厂商调用四大厂商的系统级推送通道API。

# Mi Push
According to https://dev.mi.com/console/doc/detail?pId=1292
1)   如果是在MIUI系统中，使用通知栏类型的消息，是不需要应用出于启动状态就能接收并弹出通知栏的。使用透传消息，则需要应用驻留后台才能接收，由于MIUI的自启动管理限制，所以如果应用被杀，是收不到透传消息的。
2)   如果是在非MIUI系统中，是需要应用驻留后台才能接收消息的，因此如果应用被杀死并且不能后台自启动的话，是没有办法接收消息的。
为了让app尽可能的驻留后台，小米推送服务SDK监听了网络变化等系统事件，并且有应用之间的互相唤醒，但这些措施并不能保证应用可以一直在后台驻留。

# Huawei Push
https://developer.huawei.com/consumer/cn/service/hms/catalog/huaweipush_v3.html

https://club.huawei.com/thread-16930815-1-1.html

* Need to install `Huawei Mobile Services(APK)` on device.

# Aliyun Push
According to https://juejin.im/post/5bdfab3f6fb9a049d974a61a

https://github.com/aliyun/alicloud-android-demo

# LeanCloud Push
1 每次消息发送后，被Cover到的Installations表中的如果没有发送成功，LeanCloud认为App没有在线，valid将会被set为false。当用户手机启动了App时，valid将会被set为true。

# Other Articles
https://juejin.im/post/5d25a6d3f265da1b9163bc7d

https://www.jianshu.com/p/b61a49e0279f

https://www.jianshu.com/p/d77eaca4e52a

https://www.zhihu.com/question/20628786

https://github.com/xuduo/socket.io-push

According to http://www.xmamiga.com/3443/

According to http://mouxuejie.com/blog/2018-07-28/push/
