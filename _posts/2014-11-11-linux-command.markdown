---
layout: post
title:  "Unix like常用的重要命令整理"
date:   2014-10-27 08:16:42
categories: websocket ruby
---

## Basic command
```shell
sudo -i # In CentOS, if you are not a root user, use this command to become a root user.
```

# How to make your command line tools looks better
Step 1. Install fish
Step 2. Install https://github.com/junegunn/fzf

# Domain name (DNS)
* dig - DNS lookup utility
`dig mail.163.com`


# 最重要的故障排查
```bash
$ dd bs=64k count=4k if=/dev/zero of=test oflag=dsync # 测试硬盘性能, 产品服务器上这个值至少要达到100M
```

https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md#%E6%97%A5%E5%B8%B8%E4%BD%BF%E7%94%A8

## 界面的提示为中文或者英文
vim /etc/sysconfig/i18n
LANG="zh_CN.UTF-8"
或en_US.UTF-8

已经掌握的命令就不再列出了。
# 待加强的命令
## ps -> Process Status
    $ ps
    $ ps -p 1337 -o comm=
    $ ps aux | grep postgres # 查看postgres的进程
    $ NUM=`ps M <pid> | wc -l | xargs` && expr $NUM - 1 #Mac下查看一个process下的线程数
    $ ps -ef|grep defunct 或者 ps -A -o stat,ppid,pid,cmd | grep -e '^[Zz]' # 查找僵尸进程zombie,查出的defunct的进程就是僵尸.
    $ lsof -Pnl +M -i4 | grep 5135 # 查看所有进程和端口。用grep筛选具体的某个端口, 如5135.
## System information
    $ cat /proc/cpuinfo
    $ df -H # disk info
    $ df -T # 查看硬盘格式, 数据库服务器等最好用ext4, 效率更高

## Networking
https://superuser.com/questions/621870/test-if-a-port-on-a-remote-system-is-reachable-without-telnet

```shell
telnet ip port # 查看网络的连通性
nc -zv 172.31.4.138 80 # netcat
```

## netstat
```bash
$ netstat -lnt # 查看启用的端口号
$ netstat -tulnp # Mac下是无效的
$ lsof -i -P | grep 19801 # Mac下使用
$ netstat -atunp
$ netstat -an|grep 6379|grep '123.31.11.33'|wc -l # 用于redis的6379在 123.31.11.33 这台机器上有多少连接
$ ngrep -d any -pqW byline port 5672 # 根据端口来抓包
```
http://linux.vbird.org/linux_server/0140networkcommand.php#netstat
    
## find
```bash
$ find / -name "pg_config" # 查找 pg_config 文件
$ find / -type f -name '*.iso' #查找iso普通文件    
$ find / -type f -size +500000k -exec ls -lh {} \; | awk '{ print $9 ": " $5 }' # 查询所有大小大于500M的文件
$ find DIR_NAME -type f | wc -l # 遍历某个目录, 显示该目录下的文件数量
```

`$ grep -i rack Gemfile.lock # 从一个文件中搜索相关文字, 本例是从Gemfile.lock中查找所有包含rack这个字符串的行`

## uname
print system version
$ cat /etc/redhat-release
$ uname -r

## PATH
在MAC下, `$ echo 'export PATH="/usr/local/sbin:$PATH"' >> ~/.bash_profile`
这样在新窗口 `$ $PATH`测试,可以看到 /usr/local/sbin: 被加到了开头.
打开`~/.bash_profile`可以看到它在最后一行. 修改`~/.bash_profile`可以调整$PATH的顺序.

在linux下修改~/.bash_profile, 加入如下内容可以把你想要的folder加到PATH中:

```
PATH=/usr/local/pgsql/bin:$PATH:$HOME/bin
export PATH
```

## 设置时钟
参考http://linux.vbird.org/linux_server/0440ntp.php
$ date 080310042015 # 将Linux系统时间设置为2015年8月3日10点4分
$ hwclock -w; # 更新Bios的时间，使得其与本地时间保持一致
ntp伺服器普通情况下不太用得到，就像上面这样设置一下一般就可以了。

## Cron jobs

```shell
crontab -l # user的cron jobs list
crontab -e # 编辑user的cron jobs
```

因为Cron不会自动把所有的$PATH加载，所以要`vim ~/.bash_profile`，确保node的执行文件所在的目录（`$ which node`所返回的结果，CentOS中是`/usr/local/bin`）在$PATH中。如果没在，请把以下内容加入到`~/.bash_profile`
```
PATH=$PATH:/usr/local/bin
export PATH
```

## SCP
$ scp -P <port> id_rsa.pub root@132.43.1.22:./ # 把一个文件上传到服务器
$ scp root@your_server_ip:/root/production.log ./  #从服务器下载一个文件到当前目录
If you want to use scp with port number specified, you can use `scp -P your_port_number`
如果要实现自动的下载,这时就需要有不用输入密码的机制了. 只要执行下面的命令就可以了:
`$ ssh-copy-id root@ip_b_machine # ip_b_machine是被登录的机器, 这样, 在执行了这条命令的机器上登录ip_b_machine就不用密码了`
所以要把备份机的id_rsa.pub加入到服务器的~/.ssh/authorized_keys中,详见:http://alvinalexander.com/linux-unix/how-use-scp-without-password-backups-copy
149的
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAxDI0R/CRHvCGZyDxl7eHDIJAzYINAWyfwooDXkM8bKZ928O4JUf3o2lSc18h4U+5Db/RBV5OOj6WtpjDMG1AUJv6HBfFr1K9/bdw/6SFnlQ5L+z6UU3rhIU2vYLn+Zia1jS5diW64WlVcvrUes9JQqKwIbN7V7IUXbGd1zexF71T5kF57Ulru/sycIp+mWakmbtVYXDAL5Cq7zqHoHP5YMyrRFVnek6t/+jYrP6uCytinZePYX/8kojCLTHNr/uyE/75Tjq/KKg4B7kFp7G9bUV17HmAT4azqVh1zOj+jWNb6gpcNs+w6aM4dGL0C7nBWHYF8rJ7YZRo97m4QVnW1Q== root@localhost.localdomain

## 压缩和解压缩
### Gzip
$ gzip -c logger2.log > api_nbms_logger2.log.gz # 这里-c表示输出到流,用 > 可以把流内容输出到一个指定文件,这样,就保留了原文件
$ gzip logger2.log # 这种方式会导致原文件logger2.log不复存在,生成一个logger2.log.gz文件.所以不建议用,推荐用上面的写法,即带-c参数
$ gzip -d logger2.log.gz # 解压缩

## 日志相关
### logrotate

```shell
$ touch /etc/logrotate.d/myrails #配置一下
$ vim /etc/logrotate.d/myrails
/path/to/myrails/log/production.log {
  daily
  missingok
  rotate 100
  compress
  delaycompress
  notifempty
  copytruncate
}
$ logrotate -vf /etc/logrotate.d/myrails # 立刻生成一下
```

## NFS
http://linux.vbird.org/linux_server/0330nfs.php#nfsserver

### client

```shell
$ mount 175.111.1.52:/home/fs/ /sharedfs #NFS挂载,这样,本地的/sharedfs目录就对应到了目标机器175.111.1.52:/home/fs/
$ df # 查看挂载结果
$ umount /sharedfs # 取消挂载, 如果报错device is busy,则用 umount -l
```

### server

```shell
$ rpm -qa | grep nfs # 看nfs安装了没有
$ vim /etc/exports
加入
/your/server/shared/folder     *(rw,sync,no_root_squash,fsid=0)
```

# 其他

```shell
$ > logfile # 清掉一个日志文件内容
$ nohup make & #在后台执行make操作，并输出到nohup.out。其中`make`可以是任何Linux命令
```

# 性能监控
## CPU和IO

`$ top`
Cpu(s): 10.0%us,  0.9%sy,  0.0%ni, 85.7%id,  3.3%wa,  0.0%hi,  0.1%si,  0.0%st
这里的85.7%id表示idle-空闲CPU百分比, 越大越好, 3.3%wa表示等待输入输出的CPU时间百分比, 越小越好.

## Linux内存分析

1. 首先对free -m查看结果进行分析
`free -m`

```
             total       used       free     shared    buffers     cached  
Mem:          3952       2773       178          0         130        1097  
-/+ buffers/cache:       1545       2406  
Swap:         2055          0       2055  

各参数含义：
total：总物理内存
used：已使用内存
free：完全未被使用的内存
shared：应用程序共享内存
buffers：缓存，主要用于目录方面,inode值等
cached：缓存，用于已打开的文件
-buffers/cache：应用程序使用的内存大小，used减去缓存值
+buffers/cache：所有可供应用程序使用的内存大小，free加上缓存值
 
其中：
total = used + free
-buffers/cache=used-buffers-cached，这个是应用程序真实使用的内存大小
+buffers/cache=free+buffers+cached，这个是服务器真实还可利用的内存大小
```

## link or ln
### The differences between `hard link` and `soft link`.
* In Linux, files are represented by `inodes`.
* A file is just a link to an `inode`.
* A `hard link` of a file is just another link to same `inode`.
* When you delete a file, you remove one of the link to the `inode`.
* The `inode` is only deleted when all the links to it are deleted.
* So any changes to the data of `inode` is reflected in all files (or `hard links`) that refer to the `inode`.
* A symbolic link is a link to another name (file). `ln -s source_file_or_directory a_link`.
* When you delete the source file, you cannot access the `soft link`, you will get a warning: `No such file or directory`.
* You can create a `hard link` by `ln source_file new_file`, but you can create a hard link on a directory.
