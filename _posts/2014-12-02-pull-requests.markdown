---
layout: post
title:  "Pull Requests"
date:   2014-12-02 15:16:42
categories: 
---
## Pull Requests
### Rails
    default_scope { order('created_at') }
Will cause all the query e.g: `self.company.groups.order('id DESC')` not work. This is a big bug!

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