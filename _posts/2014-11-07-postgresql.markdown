---
layout: 
title:  "PostgreSQL个人笔记"
date:   2014-10-27 08:16:42
categories: database PostgreSQL
---
## 安装
### CentOS
####  源码安装
{% highlight bash %}
$ curl -O https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.gz
$ tar zxvf postgresql-9.3.5.tar.gz
$ cd postgresql-9.3.5
$ ./configure #这里报错：configure: error: readline library not found，解决见下
$ yum -y install -y readline-devel
按照INSTALL文件的描述执行
gmake
su
gmake install
adduser postgres
mkdir /usr/local/pgsql/data
chown postgres /usr/local/pgsql/data
su - postgres
/usr/local/pgsql/bin/initdb -D /usr/local/pgsql/data
/usr/local/pgsql/bin/postgres -D /usr/local/pgsql/data >logfile 2>&1 &
/usr/local/pgsql/bin/createdb test
/usr/local/pgsql/bin/psql test
，之后
$ cd /usr/local/pgsql/bin/
$ su postgres
$ ./pg_ctl -D /usr/local/pgsql/data status #看下启动状态
$ ./pg_ctl -D /usr/local/pgsql/data start #启动PostgreSQL
$ ./pg_ctl -D /usr/local/pgsql/data restart #重启PostgreSQL
如果pg无法重启,把 /usr/local/pgsql/data/postmaster.pid 删除就可以了正常启动了
{% endhighlight %}

#### 配置
##### PostgreSQL配置
主要参考:
https://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server
http://www.revsys.com/writings/postgresql-performance.html
{% highlight bash %}
cd /usr/local/pgsql/data
vim pg_hba.conf
vim postgresql.conf
listen_addresses = '*'
max_connections
shared_buffers = 你机器内存的1/4
了解下raid10的好处: http://www.dostor.com/article/2009-12-31/2871015.shtml 深度解析RAID类型 全面透视RAID 10优势 https://zh-tw.facebook.com/notes/cerio-service-center/%E5%A6%82%E4%BD%95%E9%81%B8%E6%93%87%E8%87%AA%E5%B7%B1%E9%9C%80%E8%A6%81%E7%9A%84raid%E6%A8%A1%E5%BC%8F/10151279223314892/
{% endhighlight %}

##### 其它设置
* 设置postgreSQL在CentOS reboot的时候自动启动
把`/usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data start`加入到`/etc/rc.d/rc.local`中.

* 把postgreSQL的bin目录加到PATH中
修改~/.bash_profile, 加入如下内容:
{% highlight bash %}
PATH=/usr/local/pgsql/bin:$PATH
export PATH
{% endhighlight %}

* psql: could not connect to server: 没有到主机的路由  	Is the server running on host "172.30.1.130" and accepting TCP/IP connections on port 5432?
是因为防火墙没关闭, 解决方法: 
{% highlight bash %}
/etc/init.d/iptables stop
/etc/init.d/ip6tables stop
chkconfig iptables off
chkconfig ip6tables off
{% endhighlight %}

#### 操作系统配置
$ df -T # 查看硬盘格式, 数据库服务器等最好用ext4, 效率更高

## 常用命令
    $ psql -d postgres # Login to postgres
    
    $ psql -l # List all databases
    
    $ psql --version # 查看pg版本
    
    # \dt # List all tables.
    
    # \d+ schema_migrations # Show DDL of a table
    
    # \du
                                 List of roles
     Role name |                   Attributes                   | Member of 
    -----------+------------------------------------------------+-----------
     someuser  | Superuser, Create role, Create DB              | {}
     postgres  | Superuser, Create role, Create DB, Replication | {}
     repluser  | Replication                                    | {}
    
    详细见:
    http://www.postgresql.org/docs/9.2/static/app-createuser.html 
    http://www.postgresql.org/docs/9.3/static/sql-alterrole.html
    # ALTER ROLE davide WITH PASSWORD 'hu8jmn3';

    
    # \q 退出psql
    
    # CREATE USER postgres SUPERUSER;# if you got error: ActiveRecord::NoDatabaseError: FATAL:  role "postgres" does not exist
    http://www.moncefbelyamani.com/how-to-install-postgresql-on-a-mac-with-homebrew-and-lunchy/
  
    http://stackoverflow.com/questions/10301794/difference-between-rake-dbmigrate-dbreset-and-dbschemaload
    
    select * from pg_stat_activity where datname = 'some_production';
    
#### pg_hba.conf
    这个重要，因为涉及到允许哪些ip地址的哪些用户以什么样的方式访问哪些数据库！
    http://www.postgresql.org/docs/9.1/static/auth-pg-hba-conf.html

## 备份
如果有大表，备份费力，可以通过如下方式剔除
$ pg_dump -U postgres -Fc --exclude-table='big_table_name|not_important_big_table_name' your_production > your_production_20150728
## 还原
$ sudo -u lane pg_restore -d some_development < some_db
$ pg_restore -l some_production_0410 > 0401.list # 用这个-l可以看到这个pg_dump出来的文件有什么东西. 比如有哪些表, index等等. 方便你最后pg_restore完了比对下.
报错role "xxx" does not exist解决办法：
$ sudo -u postgres(or lane) createuser xxx

一个完整的数据库备份和还原的过程:
### 原数据库机器21
```bash
$ cd /srv/database_backup
$ nohup /usr/local/pgsql/bin/pg_dump -U postgres -Fc some_production > some_production_0410 &
```

#### 新数据库机器130
```bash
$ sudo -u postgres /usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data/ status
$ sudo -u postgres /usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data/ restart -m f # 带这两个参数才能正常的重启, 否则有client连接在是无法顺利关闭的
$ sudo -u postgres /usr/local/pgsql/bin/dropdb some_production
$ sudo -u postgres /usr/local/pgsql/bin/createdb some_production
$ cd /srv/db_35
$ scp root@172.35.11.21:/srv/database_backup/some_production_20160412 ./
$ nohup sudo -u postgres /usr/local/pgsql/bin/pg_restore -d some_production < /srv/db_35/some_production_0410 -v >> ./db_restore.log &
```

## 性能监控
### pgBadger
* 官方的包在CentOS上我发现无法解压
{% highlight bash %}
cd /root/pgbadger/
pgbadger --prefix 'postgresql.conf里面log_line_prefix的值 ' /path/to/your/pglog/*.log -o out.html
pgbadger --prefix '%t [%p]: [%l-1] ' /root/pgbadger-master/logs_from_21/postgresql-Wed_1042.log -o out_20160413_1.html
{% endhighlight %}

### 性能查看
{% highlight sql %}
select indexrelname, pg_size_pretty(pg_relation_size(indexrelid)),*
from pg_stat_user_indexes where schemaname='public' order by pg_relation_size(indexrelid) desc;

select relname, pg_size_pretty(pg_relation_size(relid)) ,*
from pg_stat_user_tables where schemaname='public' order by pg_relation_size(relid) desc;
{% endhighlight %}

## 其他
Postgresql max integer 2100000000

# Mac
## 故障处理

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

## EXPLAIN
psql# EXPLAIN SELECT  "customers".* FROM "customers"  WHERE "customers"."company_id" = 64023 AND "customers"."act" IS NULL  ORDER BY "customers"."id" DESC;
                                               QUERY PLAN                                                
---------------------------------------------------------------------------------------------------------
 Sort  (cost=26130.34..26130.34 rows=2 width=1852)
   Sort Key: id
   ->  Bitmap Heap Scan on customers  (cost=1151.27..26130.33 rows=2 width=1852)
         Recheck Cond: (company_id = 64023)
         Filter: (act IS NULL)
         ->  Bitmap Index Scan on index_customers_on_company_id  (cost=0.00..1151.27 rows=62245 width=0)
               Index Cond: (company_id = 64023)

可以看到费时在act IS NULL 这里了。