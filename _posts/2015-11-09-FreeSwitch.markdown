---
layout: post
title:  "FreeSwitch"
date:   2015-11-09 16:21:42
categories: FreeSwitch
---
特别说明：目前本人的FreeSwitch底层能力还比较初级，所写内容大家不用当真。
## 安装
目前用freeswitch-1.4.14就可以了, 1.4最高版本支持视频, 安装起来会复杂些, 暂时我用不到。
```bash
$ rpm -Uvh http://files.freeswitch.org/freeswitch-release-1-0.noarch.rpm
$ yum install sox
$ yum -y install gcc-c++ g++ zlib-devel libjpeg-devel sqlite-devel libcurl-devel pcre-devel speex-devel libedit-devel openssl-devel libshout-devel lame-devel libmpg123-devel libuuid  libuuid-devel libtool # 这些包依赖要先安装下
$ mkdir /usr/local/freeswitch
$ tar -zxvf freeswitch-1.4.14.tar.gz
$ cd ./freeswitch-1.4.14
$ ./configure --prefix=/usr/local/freeswitch # 会报错: configure: error: You need to either install libldns-dev or disable mod_enum in modules.conf
$ vim modules.conf # 搜索mod_enum, 把mod_enum这行注释掉, 然后重新执行上一行
$ make
$ make install
```

### 录音合成
```bash
$ wget http://apt.sw.be/redhat/el6/en/x86_64/rpmforge/RPMS/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
$ rpm -Uvh rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
$ yum install lame
$ /usr/local/freeswitch/bin/fs_encode -l mod_g729 $1-in.G729 $1-in.wav
$ /usr/local/freeswitch/bin/fs_encode -l mod_g729 $1-out.G729 $1-out.wav
$ sox $1-in.wav $1-out.wav -m $1.wav
$ lame $1.wav $1.mp3
$ /usr/local/freeswitch/bin/fs_encode -> fs_encode 
$ intel-ipp60071em64t-6.0p-071.noarch.rpm  mod_fs_g729
$ rpm -ivh intel-ipp60071em64t-6.0p-071.noarch.rpm
$ yum install libuuid libuuid-devel
$ cd mod_fs_g729
$ make clean
$ vim MAKEFILE # 要写正确的FS的源码库和FS的安装目录
$ make
$ make install
```

可以用如下脚本完成录音的合成。
$ touch mixin_record.sh
```
/usr/freeswitch/bin/fs_encode -l mod_g729 $1-in.G729 $1-in.wav
/usr/freeswitch/bin/fs_encode -l mod_g729 $1-out.G729 $1-out.wav
sox $1-in.wav $1-out.wav -m $1.wav
lame -h $1.wav $1.mp3
rm -rf $1.wav $1-in.wav $1-out.wav
```

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

`$ ngrep -d any -pqW byline port 8021` # 用于抓包

## 录音
FreeSwitch可以用.wav uu-law 800hz的声音,可以由mp3格式转成.