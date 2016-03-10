---
layout: post
title:  "FreeSwitch"
date:   2015-11-09 16:21:42
categories: FreeSwitch
---
特别说明：目前本人的FreeSwitch底层能力还比较初级，所写内容大家不用当真。

## 开发模式
以下三种模式并未做方案选择，开发团队应该根据情况作选择。
### 模式一：用C扩展底层
缺点：代码难写，写得不好容易出现内存泄露，影响并发。

### 模式二：用Lua处理业务
每通呼叫都会启一个Lua线程
缺点：对于连MQ或连数据库等可能不合适。

### 模式三：用Event Socket
优点：可以用各种高级语言。

$ fs_ctl -H ip  
$ console loglevel 0 （清屏）
$ console loglevel 5
$ console loglevel 7 (debug)
$ load mod_curl # 加载curl模块
$ sofia profile internal siptrace on # 如：打开日志

/usr/local/freeswitch/log

## 录音
FreeSwitch可以用.wav uu-law 800hz的声音,可以由mp3格式转成.