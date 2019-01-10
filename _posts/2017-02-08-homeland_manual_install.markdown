---
layout: post
title:  "手动安装Homeland论坛"
date:   2017-02-07 11:40:24
categories: homeland
---

Homeland官方已经有基于Docker的自动化安装的说明了, 手动安装的意义在哪里?
1 更深入了解Homeland的代码和各种支撑软件。
2 可能可以节省服务器成本。因为官方推荐4核4G的服务器 + Ubuntu, 小项目我认为用1核2G服务器 + CentOS(CentOS性价比更好), 如果运行没有问题, 可以省60%以上成本。
3 可以帮助你在本地开发环境上搭建homeland, 进而二次开发。

{% highlight bash %}
git clone https://github.com/ruby-china/homeland.git
cd homeland
现在要安装gem了。
因为我自己用的是rbenv,所以这里以rbenv为例。 请`touch .ruby-version` `touch .rbenv-gemsets`并设置好相关内容。
如果你用RVM, 请自行处理。
gem install bundler
bundle
gem安装完了, 这个时候你会发现 config/database.yml 文件还没有存在于源代码中, 所以请
{% endhighlight %}

