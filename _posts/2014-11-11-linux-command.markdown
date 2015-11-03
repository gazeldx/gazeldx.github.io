---
layout: post
title:  "Unix like常用的重要命令整理"
date:   2014-10-27 08:16:42
categories: websocket ruby
---
已经掌握的命令就不再列出了。
# 待加强的命令
## ps -> Process Status
    $ ps
    $ ps -p 1337 -o comm=
    $ ps aux | grep postgres # 查看postgres的进程
    $ NUM=`ps M <pid> | wc -l | xargs` && expr $NUM - 1 #Mac下查看一个process下的线程数
## System information
    $ cat /proc/cpuinfo

## netstat
```bash
$ netstat -lnt # 查看启用的端口号
$ netstat -tulnp # Mac下是无效的
$ lsof -i -P | grep 19801 # Mac下使用
$ netstat -atunp
$ ngrep -d any -pqW byline port 5672 # 根据端口来抓包
```
http://linux.vbird.org/linux_server/0140networkcommand.php#netstat
    
## find
    $ find / -type f -name '*.iso' #查找iso普通文件    

## uname
print system info
    $ uname -r

## 设置时钟
参考http://linux.vbird.org/linux_server/0440ntp.php
$ date 080310042015 # 将Linux系统时间设置为2015年8月3日10点4分
$ hwclock -w; # 更新Bios的时间，使得其与本地时间保持一致
ntp伺服器普通情况下不太用得到，就像上面这样设置一下一般就可以了。

# 其他
```bash
$ > logfile # 清掉一个日志文件内容
$ nohup make & #在后台执行make操作，并输出到nohup.out。其中`make`可以是任何Linux命令
```