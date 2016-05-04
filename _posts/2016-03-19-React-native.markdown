---
layout: post
title:  "React-Native"
date:   2016-03-19 14:15:31
categories: React-Native
---

# react-native的hello world
* 阅读英文文档可以提升英文能力,不过如果有无法解决的问题, 建议阅读下中文文档 => http://reactnative.cn/ 一是更直观些; 二是译者经常加入他的心得, 这些心得是有价值的.

* 在执行`$ react-native init hu`时, 很慢, 解决办法请参考 https://github.com/facebook/react-native/issues/2806 , 注意wfxiang08的回复. 
{% highlight bash %}
echo 'registry = https://registry.npm.taobao.org' > ~/.npmrc
{% endhighlight %}

* 在执行`$ react-native run-android`时报错: failed to find Build Tools revision 23.0.1, 是因为Build Tools 我当时装的是23.0.2 

* MIUI要在'设置 - 关于手机'中连按N次"MIUI版本"后进行开发者模式, 然后在'设置 - 其它高级设置 - 开发者选项'中, 打开"USB调试",
这时, 执行`$ adb devices`就可以看到这台设备了.

* 在执行`$ react-native run-android`时报错: Error: Activity class {com.hu/com.hu.MainActivity} does not exist.
解决方法: 参考 https://github.com/facebook/react-native/issues/2885 kmagiera的回复和rogerwq说的最后一句话.
{% highlight bash %}
$ cd android
$ ./gradlew :app:installDebug
$ adb install app/build/outputs/apk/app-debug.apk # 执行后报错: Failure [INSTALL_CANCELED_BY_USER] ,解决方法见: http://stackoverflow.com/questions/29444980/android-install-on-device-failure-install-canceled-by-user
在成功的执行上行后, 我的MIUI7系统上出现了我的第一个APP图标(hu),不过打开后是白屏, 解决方法: '设置 - 其它应用管理 - hu - 权限管理 - 显示悬浮窗'勾上.
关闭并再次打开APP'hu', 红屏报错,解决见 http://reactnative.cn/docs/0.21/running-on-device-android.html#content 最后内容.
下次如果再执行`$ adb install app/build/outputs/apk/app-debug.apk`还报Failure [INSTALL_CANCELED_BY_USER]的错误, 请在'开发者选项'中'选择调试应用',选到你的应用. 
然后运行你的APP后,再执行`$ adb install -r app/build/outputs/apk/app-debug.apk` -r意味着replace existing application.
{% endhighlight %}

至此, react-native的hello world完成啦!

# 继续浅尝
* 这个介绍ES6和ES5写法的文章建议阅读下:  http://bbs.reactnative.cn/topic/15/react-react-native-%E7%9A%84es5-es6%E5%86%99%E6%B3%95%E5%AF%B9%E7%85%A7%E8%A1%A8/2

* 尽量不要把一个APP的文件夹直接改名后用于其它APP, 在调试过程中我遇到一个重名的问题.



