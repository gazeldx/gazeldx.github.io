---
layout: post
title:  "Ruby Thread and Process"
date:   2014-12-17 15:16:42
categories: 
---


* 个人理解：rails和thin等web app frameworks 需要通过web server来启动，也就是说web server会启动进程，该进程会把rails加在到进程的内存，在有请求过来时将请求交给rails处理。rails进程其实就是加载它的web server的进程（或线程）。

* Unicorn 是进程式的，即如果加sleep在某个controller中，会直接导致线程卡住，所有请求全部卡住。即在此时任何浏览器访问任何页面都无效，因为unicorn默认只启动一个进程，并且一个进程只有1个线程。所以必须要通过多启动几个workers的方式（每个worker都是一个进程）来完成其他请求的并发处理（这样就不会卡住了）。而puma、thin(通过ruby xx.rb方式）启动不会出现这种问题。只在访问该controller的request会被卡住，因为他们是一个进程中包含多个线程的。

* 可以通过在任意程序中加入`::Process.pid`来查看当前的进程，也可以通过`$ ps` `$ ps -p pid` `$ ps aux`查看。

* eventmachine是通过在initialize/eventmachine.rb 中加入 Thread.new { EventMachine.run } 来完成启动的，所以它其实和rails其实是在同一个进程中。

* hutch是需要启动一个新的进程来监听rabbitmq的，可以查看它的源代码来看如何通过命令行启动一个新的进程。

* Rabbitmq的生产者们和消费者们的往往都不在同一个进程中，特别是在复杂的系统中。

