---
layout: post
title:  "Postgresql个人笔记"
date:   2014-10-27 08:16:42
categories: database postgresql
---
## Postgresql
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
    $ mkdir /var/pgsql_socket/ 
    $ ln -s /private/tmp/.s.PGSQL.5432 /var/pgsql_socket/
    
    
    http://stackoverflow.com/questions/10301794/difference-between-rake-dbmigrate-dbreset-and-dbschemaload
    
#### pg_hba.conf
    这个重要，因为涉及到允许哪些ip地址的哪些用户以什么样的方式访问哪些数据库！
    http://www.postgresql.org/docs/9.1/static/auth-pg-hba-conf.html 