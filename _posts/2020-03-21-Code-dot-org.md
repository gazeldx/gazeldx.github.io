---
layout: post
title:  "Coding"
date:   2020-03-31 21:11:11
categories: rails
---

# Time left: 2h
pass H3!

# 运维说明
## 简明排查问题版本
这次发现Rails Web服务没有开，所以网页打不开了。
先`ssh root@123.56.154.xxx`，后查看Rails Web服务是否开启，执行`ps -ef|grep 3000`。
如果看到：`root     4532     1 15 18:56 pts/1    00:02:43 puma 3.12.0 (tcp://0.0.0.0:3000) [dashboard]` 说明Rails是开着的；
如果没看到，说明没开，需要开一下，方法如下：
```shell script
cd /root/code-dot-org-staging
nohup bin/dashboard-server  /dev/null 2>&1 &
```
等3分钟，就可以访问页面了。然后输入`exit`退出ssh.
以后你们就这样操作，以防止我不方便操作。
如果还是不行，请参考我之前发的文档进行操作。

## 排查问题
发现无法访问网站时，先ssh登入服务器
`ssh root@server_ip`

`free -m` 看到如果 `free + buff/cache` 的和值相对 `total` 而言比较小，说明内存不足。需要重启。

`reboot` 重启服务器。然后 `ssh root@server_ip`，然后执行 `nginx` 启动 Nginx.

然后按如下步骤做 `重启Rails Dashboard (主程序)` 和 `重启Pegasus` 这两步。

## 重启Rails Dashboard (主程序)
查看Rails服务是否开启：
`ps -ef|grep 3000`看到

`root     4532     1 15 18:56 pts/1    00:02:43 puma 3.12.0 (tcp://0.0.0.0:3000) [dashboard]`说明Rails是开着的。

先通过执行`kill -9 4532`关掉它。`4532`是进程ID, 每次启动后都不一样。
然后执行如下启动它。
```shell script
cd /root/code-dot-org-staging
nohup bin/dashboard-server  /dev/null 2>&1 &
```

## 重启Pegasus(Sinatra程序，次要程序，通过3099端口访问)
`ps -ef|grep 3090`看到

`root     24387     1  0 Mar29 ?        00:00:54 thin server (0.0.0.0:3090)`
先通过执行`kill -9 24387`关掉它。`24387`是进程ID, 每次启动后都不一样。
然后执行如下启动它。
```shell script
cd /root/code-dot-org-staging/pegasus/
thin start -p 3090 -d
```

## 日志
查看日志：
```shell script
tail -n 500 /var/log/messages
tail -n 500 /root/code-dot-org-staging/nohup.out
tail -f /var/log/nginx/error.log
```

## 数据库备份
```
mysqldump --user root dashboard_development > dashboard_development_20220417.sql
mysqldump --user root pegasus_development > pegasus_development_20220417.sql
```

## 目前主要问题
* 许多图片还没有经过缓存。缓存后加载速度可加快。
* 许多js还没有经过压缩，导致初次加载时太慢。
* Rails没有进行多进程处理，对并发处理不理想。
* 注册和登录不能用。因为csrf问题。

## 关键问题解决方案
### 访问网站太慢
#### 原因1
原因是网站引入的javascript文件没有经过缓存，每次都要加载。需要把Nginx配置正确。目前的Nginx配置是正确的，在`/etc/nginx/nginx.conf`中。
大略是这样的：
```nginx
user root;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 2048;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    include /etc/nginx/conf.d/*.conf;

    upstream rails {
        server 127.0.0.1:3000 fail_timeout=0;
        #server 127.0.0.1:3001 fail_timeout=0;
        #server 127.0.0.1:3002 fail_timeout=0;
        #server 127.0.0.1:3003 fail_timeout=0;
    }

    proxy_cache_path  /var/cache/nginx levels=1:2 keys_zone=default:8m max_size=1000m inactive=30d;
    proxy_temp_path   /var/cache/nginx/tmp;

    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;

        root /root/code-dot-org-staging/dashboard/public;
        try_files $uri/index.html $uri.html $uri @app;

 	 location @app {
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
             proxy_set_header Host $http_host;
             proxy_redirect off;
             proxy_pass http://rails;
             proxy_cache default;
             proxy_cache_lock on;
             proxy_cache_use_stale updating;
             add_header X-Cache-Status $upstream_cache_status;
        }

        error_page 404 /404.html;
        location = /40x.html {}

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {}
    }

    upstream sinatra {
        server 127.0.0.1:3090 fail_timeout=0;
    }

    server {
        listen       3099;
        listen       [::]:3099;
        server_name  pegasus;

        location / {
            proxy_pass http://sinatra;
        }

        error_page 404 /404.html;
        location = /40x.html {}

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {}
    }

    gzip              on;
    gzip_comp_level   8;
    gzip_min_length   1280;
    gzip_buffers      16 8k;
    gzip_types        text/plain text/css application/json application/javascript application/x-javascript text/javascript text/xml application/xml application/rss+xml application/atom+xml application/rdf+xml;
    gzip_vary         on;
}
```

# OSX Installation
```shell script
brew install redis
brew install rbenv
rbenv install 2.5.0
brew install coreutils
brew install sqlite
brew install enscript
brew install gs
brew install mysql@5.7
brew install imagemagick
brew cask install phantomjs
brew install openssl
```

When install nvm, you need to use https://github.com/edc/bass because https://github.com/nvm-sh/nvm/issues/303.  
Look at edc's answer.

`npm install -g yarn@1.16.0`

## Install project
Look at the `# CentOS Installation` part to see how to get the full source code `staging.zip` (6GB).
```shell script
cd dashboard
gem install bundler:1.17.1
bundle install # You will got some erros. Then fix these errors with:

gem update --system
gem install libv8 -v 3.16.14.15 -- --with-system-v8
gem install mysql2 -v '0.5.2' -- --with-cflags=\"-I/usr/local/opt/openssl/include\" --with-ldflags=\"-L/usr/local/opt/openssl/lib\"
For therubyracer bundle issue:
brew install v8@3.15
gem install libv8 -v 'YOUR_VERSION' -- --with-system-v8
gem install therubyracer -v 'YOUR_VERSION' -- --with-v8-dir=/usr/local/opt/v8@3.15

bundle install
cd /path/to/project/root
bundle exec rake install:hooks
bundle exec rake install
bundle exec rake build (Only run at the first time!)
```

# CentOS Installation
* `cat /proc/cpuinfo`, `top` make sure at least 4 core 8G memory.

* Download `curl -O -L https://github.com/code-dot-org/code-dot-org/archive/staging.zip`. 
This file is more that 6GB, but normally it won't take you a long time because of the high brand-width of server.
You may need this file too in you local machine. But if you don't have a high brand-width in local, 
you can download it in server first, then 
`nohup rsync -avP  root@server_ip:/root/staging.zip ./coding.zip 2>&1 &`  

* At most time you needn't to escape the GFW in the server because when you need some big packages, 
you can download it in your local machine and then upload the package by `SCP`.  

## Install Redis
* Follow https://linuxize.com/post/how-to-install-and-configure-redis-on-centos-7/ to install Redis.

```shell
sudo yum install -y epel-release yum-utils
sudo yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum-config-manager --enable remi
sudo yum install -y redis
sudo systemctl start redis
sudo systemctl enable redis
sudo systemctl status redis
```

## Install rbenv

```shell script
yum install -y git bzip2 openssl-devel readline-devel zlib-devel
wget https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-installer -O rbenv-installer
sh rbenv-installer
vim ~/.bashrc # Add the next line to it:
export PATH=/root/.rbenv/bin:$PATH
# If it is too slow to run `rbenv install --verbose 2.5.0`, then you can run:
curl https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.0.tar.bz2 -O ruby-2.5.0.tar.bz2
in this machine or in your local. Then copy this file to ~/.rbenv/cache

~/.rbenv/bin/rbenv init # Then follow the instruction, append:
eval "$(rbenv init -)"
to ~/.bash_profile

rbenv global 2.5.0
ruby -v # Test
```

## Install node and yarn

```shell script
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash # Install nvm
nvm install 8.17.0
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
sudo rpm --import https://dl.yarnpkg.com/rpm/pubkey.gpg
sudo yum install -y yarn
yarn --version
```

## Install MySQL
* Visit https://dev.mysql.com/downloads/mysql/5.6.html?os=31 and select 5.7 and download `(mysql-5.7.29-1.el7.x86_64.rpm-bundle.tar)`.

Don't use a low version of mysql! You must install mysql version over 5.7.8! Or you will get an json syntax error!

```shell script
tar xvf mysql-5.7.29-1.el7.x86_64.rpm-bundle.tar
yum localinstall -y mysql-community-common-5.7.29-1.el7.x86_64.rpm # If you got error: conflicts with file from package mariadb-libs, plese refer to https://stackoverflow.com/questions/27113696/mysql-wont-install-in-centos-due-to-conflict-with-mariadb
yum localinstall -y mysql-community-libs-5.7.29-1.el7.x86_64.rpm
yum localinstall -y mysql-community-devel-5.7.29-1.el7.x86_64.rpm
yum localinstall -y mysql-community-client-5.7.29-1.el7.x86_64.rpm
yum localinstall -y mysql-community-server-5.7.29-1.el7.x86_64.rpm
rpm -qa | grep mysql
systemctl enable mysqld
systemctl start mysqld
systemctl status mysqld
mysql -u root # You should login successfully without password. If you meet `ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)`, then:
vim /etc/my.cnf # Add: 
skip-grant-tables # after [mysqld] 
systemctl restart mysqld
```

## Install Others

```shell script
yum install -y enscript
yum install -y sqlite-devel
```

## Install phantomjs
According to https://www.liquidweb.com/kb/how-to-install-phantomjs-on-centos-7/

```shell script
yum install -y fontconfig
yum install -y freetype-devel
yum install -y fontconfig-devel
```

## Install ImageMagick
https://tecadmin.net/install-imagemagick-on-centos-rhel/

```shell script
yum install -y gcc php-devel php-pear
yum install -y ImageMagick
yum install -y ImageMagick-devel # If you got error of conficting with ghostscript, please run `yum remove ghostscript`.
```

## Install Project

```shell script
vim ~/.gemrc # Add this line:
gem: --no-document

unzip staging.zip

cd dashboard
gem install bundler:1.17.1
# gem update --system
bundle install --verbose # Maybe you sometimes you need to change the source to https://gems.ruby-china.com
cd /path/to/project/root
git init

bundle exec rake install:hooks
# This step will take a long time. So remember to read https://github.com/code-dot-org/code-dot-org/blob/staging/SETUP.md#overview first.
# If you got error, you can `mysql> drop database dashboard_development; drop database dashboard_test;`, then `systemctl restart mysqld`.  
bundle exec rake install
bundle exec rake build # Only run once in the first time and may take a little long time.

bin/dashboard-server
curl -iL http://localhost-studio.code.org:3000/

cd pegasus
thin start -p 3090 -d
```

## Install Nginx
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7

```shell script
$ yum install epel-release -y
$ yum install -y nginx
$ systemctl start nginx # 'restart', 'stop' are also available.
$ systemctl status nginx # Vew nginx status
$ vim /etc/nginx/nginx.conf
```

* Need to pay attention to SecurityGroup. Make sure inbound port 80, 443(if https), and 3099 is allowed.
* Then add a A record to your server IP.
* Visit your domain, you can see your website is accessible. But many files like `http://example.com/assets/js/code-studio.js` are 404.
That's because these files not still not exist in `dashboard/public/blockly`. 
You can run `npm run build`(later I will tell you how to build and deploy with compressed js like min.js) the generate the js files if `apps/build/package` doesn't have the js you need.
Then run `ln -s /root/code-dot-org-staging/apps/build/package blockly` if `dashboard/public/blockly` not exists. 

## Change code to make website work well
* Now you can see all links are not `example.com` but `localhost-studio.code.org`. 
You should change: 

* log will be `tail -f /var/log/messages`.
## Changed code
Slow is because js not cached. Follow https://mattbrictson.com/nginx-reverse-proxy-cache 

### Configuration changes
Add `optimize_webpack_assets: true` to `locals.yml`. This will use mini js files with the name like: `indexwp3860e3011d6213ae617b.min.js`.
Please read `apps/docs/build.md`. 

### Dashboard changes
`config/environment/production.rb`
`config.log_level = debug`

`dashboard/config/database.yml` line 55
dashboard_development

`dashboard/config/database.yml` line 55

`locals.yml`
line 11: dashboard_enable_pegasus: false

`config.yml.erb`
dashboard_port: 3000
dashboard_workers: 4

line 516: db_writer: mysql://root@localhost/
line 407: pegasus_port: 3001

`bin/dashboard-server` line 17:
`system "RAILS_ENV=production bundle exec #{rerun} puma -t 5:5 -w 4 -p #{CDO.dashboard_port}"`
After run `bin/dashboard-server` please be patient to wait 1 minute to see the `Listening on 0.0.0.0:3000 ...`.

`lib/cdo.rb` 
line 94, 106: studio.code.org => studio.example.com
line 110: studio_url use "//example.com/#{path}"

And add

```ruby
def studio_url2(path = '', scheme = '')
  "//example.com#{path}"
end

def code_org_url(path = '', scheme = '')
  "//example.com:3099/#{path}"
end
```

`dashboard/app/helpers/course_block_helper.rb`
line 23: url: CDO.studio_url("s/#{id}"),


`lib/cdo/hamburger.rb`
Comment off 
```ruby
{title: I18n.t("#{loc_prefix}stats"), url: CDO.code_org_url("/promote"), id: "header-en-stats"},
{title: I18n.t("#{loc_prefix}help_us"), url: CDO.code_org_url("/help"), id: "header-en-help"},
{title: I18n.t("#{loc_prefix}about"), url: CDO.code_org_url("/about"), id: "header-en-about"},
```
non_en_signed_out_links use `CDO.studio_url2`
```ruby
about_intl = [
#{title: I18n.t("#{loc_prefix}about"), url: CDO.code_org_url("/international/about"), id: "header-non-en-about"}
{title: 'A', url: CDO.studio_url2("/s/coursea-2019"), id: "header-course-a"},
{title: 'B', url: CDO.studio_url2("/s/courseb-2019"), id: "header-course-b"},
{title: 'C', url: CDO.studio_url2("/s/coursec-2019"), id: "header-course-c"},
{title: 'D', url: CDO.studio_url2("/s/coursed-2019"), id: "header-course-d"},
{title: 'E', url: CDO.studio_url2("/s/coursee-2019"), id: "header-course-e"},
{title: 'F', url: CDO.studio_url2("/s/coursef-2019"), id: "header-course-f"},
]
```

`app/views/layout/_header.html.haml`
line 79 `code_org_url` to `studio_url = link_to(image_tag('logo.png'), CDO.code_org_url, {id: "logo_home_link"})`

* `app/views/devise/registrations/_sign_up.html.haml`
`.old_form{style: "display: none"}` and `.new_form`.

### pegasus changes
`pegasus/helpers.rb`
line 43:
```ruby
def studio_url(path = '')
  port = (!rack_env?(:development) || CDO.https_development) ? '' : ":#{CDO.dashboard_port}"
  "//#{canonical_hostname('studio.example.com')}#{3000}/#{path}"
end

def code_org_url(path = '')
  port = (!rack_env?(:development) || CDO.https_development) ? '' : ":#{CDO.pegasus_port}"
  "//#{canonical_hostname('code.example.com')}#{3099}/#{path}"
end
```

### JS part
`nvm`
`npm run build:dist`
`src/templates/progress/ProgressBubble.jsx`
line 267:
'localhost-studio.code.org' for production. port 3000 for development
```javascript
href={href.replace(
    'localhost-studio.code.org:3000',
    'studio.example.com'
)}
```

### 其它说明
#### 关于Build
请一定读一下 `apps/docs/build.md`.

#### 加载的js大文件没有经过压缩。
* 用`npm run build:dist`生成的压缩后的目标文件`build/package/js/code-studio-commonwpf....js`，会被`ln -s`软链接到`dashboard/public/blockly/`，
在`lib/cdo/asset_helper.rb`中会`dashboard/public/blockly/js/manifest.json`，`application.html.haml`中用到`webpack_asset_path`，会调用压缩后的js。
* Add `optimize_webpack_assets: true` to `locals.yml`(这一步不能漏，否则调用的不是压缩后的文件)。

####  检验public/assets/js下文件每次都是最新，不用缓存的办法
修改`production.rb`中
```ruby
config.public_file_server.headers = {'Cache-Control' => "public, max-age=86400, s-maxage=43200"}改成
config.public_file_server.headers = {'Cache-Control' => "no-cache"}
```
