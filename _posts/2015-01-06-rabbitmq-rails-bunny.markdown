---
layout: post
title:  "Rabbitmq with Websocket-rails and Bunny"
date:   2015-01-06 15:16:42
categories: 
---

    exchange = channel.topic("hutch", durable:	true)

这句话要求如果exchange 'hutch'已经存在于系统中，你的写法必须和已经存在的相同，否则报redeclare exchange 的错误。

* Rabbitmq的生产者们和消费者们的往往都不在同一个进程中，可能在不同的机器上，特别是在复杂的系统中。


* hutch是需要启动一个新的进程来监听rabbitmq的，可以查看它的源代码来看如何通过命令行启动一个新的进程。http://localhost:15672/#/connections 可以看到rabbitmq的连接情况。