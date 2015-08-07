---
layout: post
title:  "Pull Requests"
date:   2014-12-02 15:16:42
categories: 
---
## Pull Requests

### node-postgres
https://github.com/brianc/node-postgres/wiki/Example
https://github.com/brianc/node-postgres/wiki/Client#method-end
https://github.com/brianc/node-postgres?_ga=1.53837800.1114043387.1423559058

首页的例子是有client.end的，但在example中却没有client.end，而wiki/client中是说不应该有client.end。是不是应该去掉client.end呢？

### Rails
    default_scope { order('created_at') }
Will cause all the query e.g: `self.company.groups.order('id DESC')` not work. This is a big bug!

如果建表的时候id: false，这时（可能是针对self.primary_key = :number的情况，但应该不是）
phone_number = PhoneNumber.find_by_number_and_company_id(param[:number], param[:company_id])
phone_number.update或者destroy等操作，会丢失param[:company_id]的搜索条件，就是说本来应该只更新或者删除一条数据，结果删除了多条数据了。

### Grape
mount Nbms::API => '/'这种绑定到root的方式会导致request.path_info缺失了/了，本来应该是/users，现在却成了users。

### websocket-rails
Exception: 
`RuntimeError (eventmachine not initialized: evma_install_oneshot_timer):
  eventmachine (1.0.3) lib/eventmachine.rb:323:in `add_oneshot_timer'`

解决办法见： http://stackoverflow.com/questions/25657405/websockets-not-working-in-my-rails-app-when-i-run-on-unicorn-server-but-works-o  

Pull requests:

主要是创建config/initializers/eventmachine.rb，并加入：
    Thread.new { EventMachine.run } unless EventMachine.reactor_running? && EventMachine.reactor_thread.alive?


### Slim
* Pull request to slim that the module Helpers should better be deleted.
* Support if in hash

### Spring
* production bug

### Lotus
* Can't run.

### Hutch
$ hutch --require path/to/rails-app  # loads a rails app
Will run the rails app again. This is not good.
~~~
$ hutch --require /Users/lane/projects/ucweb
2014-12-17T02:19:50Z 1175 INFO -- hutch booted with pid 1175
2014-12-17T02:19:50Z 1175 INFO -- found rails project (.), booting app
hello EventMachine ----------
2014-12-17T02:19:52Z 1175 INFO -- found rails project (/Users/lane/projects/ucweb), booting app
~~~

### Settingslogic
require "yaml"
require "erb"
require 'open-uri'

"" to ''