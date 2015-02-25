---
layout: post
title:  "Linux常用的重要命令整理"
date:   2014-10-27 08:16:42
categories: websocket ruby
---
已经掌握的命令就不再列出了。
## 待加强的命令
#### ps -> Process Status
    $ ps
    $ ps -p 1337 -o comm=
    $ ps aux | grep postgres # 查看postgres的进程
    $ NUM=`ps M <pid> | wc -l | xargs` && expr $NUM - 1 #Mac下查看一个process下的线程数

#### netstat
http://linux.vbird.org/linux_server/0140networkcommand.php#netstat

```bash
$ netstat -tulnp
$ netstat -atunp
$ ngrep -d any -pqW byline port 5672# 根据端口来抓包

```

    
#### find
    $ find / -type f -name '*.iso' #查找iso普通文件    

#### uname    
print system info
    $ uname -r
    
#### CentOS
```
   $ cat /etc/redhat-release 
   $ yum update
   $ yum install git
   
报错：No package git available.

解决见： http://blog.csdn.net/laiahu/article/details/7516939
   
   rpm -Uvh http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
   
git clone时报错：

   Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).
   
   fatal: Could not read from remote repository.
   
可能是因为 公钥没有上传到gitlab上。解决见：  https://help.github.com/articles/generating-ssh-keys/#platform-linux

还是不行的话就看：

解决见： http://stackoverflow.com/questions/21518074/permission-denied-publickey-gssapi-keyex-gssapi-with-mic-on-openshift

这时就要安装ruby了，因为rhc是要用rubygems的。见：https://rvm.io/

   Remove the keys: $ rhc sshkey-remove 
   
   https://developers.openshift.com/en/getting-started-rhel-centos.html#client-tools
   
   $ gem install rhc   

这时你要在openshift.com注册了，不过reCAPTCHA是google提供的，要翻墙才行。可以用海bei的VPN。

然后 https://help.github.com/articles/generating-ssh-keys/#platform-linux

http://docs.openshift.com/online/user_guide/ssh_keys.html#tutorial-creating-and-uploading-ssh-keys

    $ rhc sshkey add mysshkey ~/.ssh/id_rsa.pub
   
   
```    