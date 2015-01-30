---
layout: post
title:  "WebSocket Ruby实现的资料和心得整理"
date:   2014-10-27 08:16:42
categories: websocket ruby
---
## WebSocket Ruby
EventMachine 是否支持Ruby2.0？应该支持吧，因为我用websocket-rails的时候不报错。

## websocket-rails
~~~
javascript:
  var dispatcher = new WebSocketRails('localhost:3000/websocket');
  dispatcher.on_open = function (data) {
    console.log('-------Connection has been established: ', data);
  }

  dispatcher.trigger('hellowork', {name: 'Start taking advantage of WebSockets'});

  dispatcher.bind('hellowork_success', function (res) {
    console.log('successfully created hellowork_success' + res.name);
  });
~~~

~~~
class HelloController < WebsocketRails::BaseController
  def hellowork
    puts "---hellowork---- and message is #{message[:name]}"
    sleep(20)
    send_message :hellowork_success, message
  end
end
~~~

~~~
subscribe :hellowork, :to => HelloController, :with_method => :hellowork
~~~

~~~
class ConnectController < WebsocketRails::BaseController
  def client_connected
    puts "---client_connected----"
    send_message :connect_success, { message: 'Welcome! We are connected!' }
  end

  def client_disconnected
    puts "---disconnected ----"
    send_message :disconnect_success, { message: 'Yeah! We are disconnected!' }
  end
end
~~~

### 注意事项：
new WebSocketRails('localhost:3000/websocket'); 这里的URL一定要通过localhost:3000来访问网站，否则会因为session不能跨域名共享而抱current_user id找不到的错误。

### 问题：
1、(Resolved.)因为我加了sleep(20)，导致整个rails  web服务被卡死。成了单线程的了。这是不可忍受的。(补充：这个应该不是问题。因为在rails自己的controller中加sleep也是同样的效果。后来查了一下，sleep是暂停当前线程。但在sinatra代码中写sleep不会影响其他浏览器的响应，据说sinatra通过ruby xxx.rb的方式启动，是多线程的。难道rails却变成了单线程的了？还是感觉奇怪。)

2、(基本解决)每次点击该页面(包含：var dispatcher = new WebSocketRails('localhost:3000/websocket');)都会生成一个connection.如果按ctrl+R，会刷新页面，这时会关闭所有连接,并重新建立一个连接。如果点击了其他页面，并不会关闭原有连接。但ctrl+R会关闭并重新建立一个连接。
如果关闭浏览器，会关闭所有连接。

    解决方案：用一个新打开的页面进行WebSocket通讯，其他页面不要和websocket通讯。

#### 3、用puma和unicorn跑，都出现
(Resolved)
`RuntimeError (eventmachine not initialized: evma_install_oneshot_timer):
  eventmachine (1.0.3) lib/eventmachine.rb:323:in `add_oneshot_timer'`

解决办法见： http://stackoverflow.com/questions/25657405/websockets-not-working-in-my-rails-app-when-i-run-on-unicorn-server-but-works-o  

主要是创建config/initializers/eventmachine.rb，并加入：
    Thread.new { EventMachine.run } unless EventMachine.reactor_running? && EventMachine.reactor_thread.alive?
  
4 (Resolved.) 
 `Unexpected error while processing request: deadlock; recursive locking `
 Add `config.middleware.delete Rack::Lock` to `production.rb`


```ruby


```

