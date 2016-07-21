---
layout: post
title:  "Ruby Unix Command"
date:   2016-05-17 16:56:42
categories: 
---

# 在Ruby中调用Unix命令
请重点参考 
http://ruby-doc.org/core-2.3.1/Kernel.html#method-i-system
http://ruby-doc.org/core-2.3.1/Kernel.html#method-i-spawn
http://ruby-doc.org/core-2.3.1/Kernel.html#method-i-60
http://ruby-doc.org/core-2.2.0/Process.html#method-c-wait
http://tech.natemurray.com/2007/03/ruby-shell-commands.html

注意: 如果你的程序中有超时退出, 并且如果bash没有执行完成, 会导致zombie进程的产生。




