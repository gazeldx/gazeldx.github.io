---
layout: post
title:  "PostgreSQL个人笔记"
date:   2014-10-27 08:16:42
categories: database PostgreSQL
---
## PostgreSQL
### Mac
    $ psql -d postgres # Login to postgres
    
    $ psql -l # List all databases
    
    # \dt # List all tables.
    
    # \d+ schema_migrations # Show DDL of a table
    
    $ CREATE USER postgres SUPERUSER;# if you got error: ActiveRecord::NoDatabaseError: FATAL:  role "postgres" does not exist
    http://www.moncefbelyamani.com/how-to-install-postgresql-on-a-mac-with-homebrew-and-lunchy/
    
    http://stackoverflow.com/questions/7975556/how-to-start-postgresql-server-on-mac-os-x
    $ less /usr/local/var/postgres/postgresql.conf
    $ ps aux|grep postgres
    $ pg_ctl -D /usr/local/var/postgres status
    $ pg_ctl -D /usr/local/var/postgres restart
    
    TroubleShoot: could not connect to server: No such file or directory Is the server running locally and accepting connections on Unix domain socket "/var/pgsql_socket/.s.PGSQL.5432"?
    PG::ConnectionBad - could not connect to server: Connection refused
    http://stackoverflow.com/questions/19828385/pgconnectionbad-could-not-connect-to-server-connection-refused
    是因为关机时postgres没有正确的关闭！
    $ cd /usr/local/var/postgres
    $ rm postmaster.pid
    $ pg_ctl -D /usr/local/var/postgres status
    $ 把取得的进程PID杀死，之后pg应该会自动重生！

    $ mkdir /var/pgsql_socket/ 
    $ touch /private/tmp/.s.PGSQL.5432
    $ ln -s /private/tmp/.s.PGSQL.5432 /var/pgsql_socket/
    
    
    
    http://stackoverflow.com/questions/10301794/difference-between-rake-dbmigrate-dbreset-and-dbschemaload
    
#### pg_hba.conf
    这个重要，因为涉及到允许哪些ip地址的哪些用户以什么样的方式访问哪些数据库！
    http://www.postgresql.org/docs/9.1/static/auth-pg-hba-conf.html

## 备份
如果有大表，备份费力，可以通过如下方式剔除
$ pg_dump -U postgres -Fc --exclude-table='big_table_name|not_important_big_table_name' your_production > your_production_20150728
## 还原
$ sudo -u lane pg_restore -d some_development < some_db

报错role "xxx" does not exist解决办法：
$ sudo -u postgres(or lane) createuser xxx

## 其他
Postgresql max integer 2100000000

## Mac homebrew 安装完postgresql后的提示信息
Lanes-MacBook-Air-2:ucweb lane$ brew install postgresql
==> Downloading https://homebrew.bintray.com/bottles/postgresql-9.4.4.yosemite.bottle.tar.gz
######################################################################## 100.0%
==> Pouring postgresql-9.4.4.yosemite.bottle.tar.gz
==> /usr/local/Cellar/postgresql/9.4.4/bin/initdb /usr/local/var/postgres
==> Caveats
If builds of PostgreSQL 9 are failing and you have version 8.x installed,
you may need to remove the previous version first. See:
  https://github.com/Homebrew/homebrew/issues/2510

To migrate existing data from a previous major version (pre-9.4) of PostgreSQL, see:
  https://www.postgresql.org/docs/9.4/static/upgrading.html

To have launchd start postgresql at login:
  ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents
Then to load postgresql now:
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
Or, if you don't want/need launchctl, you can just run:
  postgres -D /usr/local/var/postgres
==> Summary
🍺  /usr/local/Cellar/postgresql/9.4.4: 3014 files, 40M