---
layout: 
title:  "PostgreSQL个人笔记"
date:   2014-10-27 08:16:42
categories: database PostgreSQL
---

## 安装
### 安装前确认
```bash
$ dd bs=64k count=4k if=/dev/zero of=test oflag=dsync # 测试硬盘性能, 产品服务器上这个值至少要达到100M
$ df -T # 在安装前先查看硬盘格式, 数据库服务器等最好用ext4, 效率更高
```

### CentOS
####  源码安装
{% highlight bash %}
$ curl -O https://ftp.postgresql.org/pub/source/v9.5.2/postgresql-9.5.2.tar.gz
$ tar zxvf postgresql-9.5.2.tar.gz
$ cd postgresql-9.5.2
$ ./configure #这里报错：configure: error: readline library not found，解决见下
$ yum -y install -y readline-devel
按照INSTALL文件的描述执行
make
su
make install
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
$ /opt/pgsql/9.5.2/bin/postmaster -D /pgdata95 # 这是另外一种启动方式
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
增加了pg 9.5专用参数max_wal_size = 1GB (9.5)
min_wal_size = 80MB (9.5)
wal_keep_segments = 1000 (9.5)
废除了一个参数checkpoint_segments = 128-256  (below 9.5) 
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

* psql: could not connect to server: 没有到主机的路由  	Is the server running on host "172.32.1.130" and accepting TCP/IP connections on port 5432?
是因为防火墙没关闭, 解决方法: 
{% highlight bash %}
/etc/init.d/iptables stop
/etc/init.d/ip6tables stop
chkconfig iptables off
chkconfig ip6tables off
{% endhighlight %}

#### 操作系统配置
$ df -T # 查看硬盘格式, 数据库服务器等最好用ext4, 效率更高
一、操作系统安装

1.文件系统:ext4

2.磁盘分布
* 1)boot: 2GB
2)swap：

RAM	                             Swap Space
Between 1 GB and 2 GB	    1.5 times the size of the RAM
Between 2 GB and 16 GB	    Equal to the size of the RAM
More than 16 GB	            16 GB

一般16GB
3)/: 其它全部

3.磁盘布局

PG软件目录：本地磁盘/opt/pgsql9.5 或 /usr/local/pgsql/
* PGDATA: 磁盘阵列，mout array_dev /opt/pgdata95 (这里的mout应该改为mount)

二、系统参数配置

1.OS参数设置

##### vi  /etc/sysctl.conf

* fs.aio-max-nr = 1048576
* fs.file-max = 6815744

kernel.shmmax = 4294967295                  # 物理内存一半
kernel.shmall = 2097152                     # 物理内存大小除以分页大小。# getconf PAGE_SIZE
                                            # 32GB/4096 ;select 32*(1024*1024*1024)::bigint/4096 as SHMALL;
kernel.shmmni = 4096                        # SHMMNI参数：设置系统级最大共享内存段数量,default 4096。
kernel.msgmax = 65536
kernel.msgmni = 2005
kernel.msgmnb = 65536
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576


###### vi /etc/security/lmits.conf
###### End of file
###### pg limit new add
postgres       soft    nproc           4096
postgres       hard    nproc           16384
postgres       soft    nofile          65535
postgres       hard    nofile          65535
postgres       soft    stack           10240
postgres       hard    stack           32768


###### vi /etc/pam.d/login
session required /lib/security/pam_limits.so

2.postgresql调整
1)postgresql扩展安装
cd postgresql-source/contrib/pg_stat_statements

2)修改参数

PGDATA目录postgresql.conf

max_connections = 根据实际情况确定
superuser_reserved_connections = 根据实际情况确定
shared_buffers = 25%物理内存，上限40%
* shared_preload_libraries = 'pg_stat_statements'
* pg_stat_statements.max = 1000
* pg_stat_statements.track = all
work_mem = 64MB根据连接数确定	
* effective_cache_size = (shared_buffers + os cache的含义?)
* maintenance_work_mem = 512MB(create index)
checkpoint_segments = 32                # in logfile segments, min 1, 16MB each	
checkpoint_completion_target = 0.9
* fsync = on 
* vacuum_cost_delay = 10                  # 0-100 milliseconds
* vacuum_cost_limit = 10000               # 1-10000 credits
* 50 bgwriter_delay = 10ms                   # 10-10000ms between rounds
wal_sync_method = fdatasync             # the default is the first option
* -1 wal_buffers = 16384kB                   # min 32kB, -1 sets based on shared_buffers
wal_writer_delay = 10ms                 # 1-10000 milliseconds
+ full_page_writes = off

##### Postgresql 开启Log分析
http://daigong.sinaapp.com/?p=67
log_min_duration_statement = 1000     # -1 不log sql  0 log 所有sql，如果大于1 ，以ms单位，记录超过该时间的sql，也就是我们说的查找sql中瓶颈

需要注意的是该属性与其他俩个属性有互斥，一定要确保

log_duration = off  #这个的含义是记录所有duration时间

log_statement = 'none' #这个属性代表记录sql的类型

如果这俩个属性开启，你会发现你的log中有一次查询会有很多时间，其中很多是你不关心的。

$ pg_ctl reload -D data #当配置文件改变时，使用. 这样数据库不会重启，只会发出一个信号，让其重新读取log

## 常用命令
    $ sudo -u postgres psql 
    $ \connect project_production # 切换数据库
    $ psql -d postgres # Login to postgres
    
    $ psql -l # List all databases
    
    $ psql --version # 查看pg版本
    
    # \dt+ # List all tables.
    
    # \d+ schema_migrations # Show DDL of a table
    
    # \du
                                 List of roles
     Role name |                   Attributes                   | Member of 
    -----------+------------------------------------------------+-----------
     someuser  | Superuser, Create role, Create DB              | {}
     postgres  | Superuser, Create role, Create DB, Replication | {}
     repluser  | Replication                                    | {}
    
    详细见:
    http://www.postgresql.org/docs/9.3/static/sql-createrole.html
    http://www.postgresql.org/docs/9.3/static/sql-alterrole.html
    http://www.postgresql.org/docs/9.2/static/app-createuser.html 
    # /usr/local/pgsql/bin/psql -d postgres -U postgres
    # CREATE ROLE someuser SUPERUSER CREATEDB CREATEROLE LOGIN;
    # ALTER ROLE someuser WITH PASSWORD 'hu8jmn3';
    # ALTER ROLE someuser LOGIN;
     
    
    # \q 退出psql
    
    # CREATE USER postgres SUPERUSER;# if you got error: ActiveRecord::NoDatabaseError: FATAL:  role "postgres" does not exist
    http://www.moncefbelyamani.com/how-to-install-postgresql-on-a-mac-with-homebrew-and-lunchy/
  
    http://stackoverflow.com/questions/10301794/difference-between-rake-dbmigrate-dbreset-and-dbschemaload
    
    select * from pg_stat_activity where datname = 'some_production'; # 查看clients
    
#### pg_hba.conf
    这个重要，因为涉及到允许哪些ip地址的哪些用户以什么样的方式访问哪些数据库！
    http://www.postgresql.org/docs/9.1/static/auth-pg-hba-conf.html

## clients数量过多会导致postgresql直线性能下降
在大并发时就会有问题.
连接数据库的clients数量(用SELECT count(*) FROM pg_stat_activity;可以查看)在120内就够了. 不必过多, 够用就好. 
因为clients都是进程,在postgresql的服务器上用`ps -ef|grep postgres` 可以看到,进程多了是实打实的消耗资源.
rails的database.yml中pool应填写puma的max_threads值, rails启动后会按puma的min_threads值分配连接数, 够用就不会多分配一个, 也不会少一个, 连接数会是workers * min_threads.
不够用时, 就会按最多max_threads去分配, 当系统卡时, 所有连接都不会被释放.
这时候 SELECT count(*) FROM pg_stat_activity; (本次故障时连接数>200, 当时光WEB就占用了workers * max_threads = 5 * 32 = 160个).
之后我把puma的max_threads改为16, rails的database.yml中pool也改为16, 并用$ kill -s SIGTERM `cat /var/run/ucweb.pid` 把WEB杀死, 
并重新启动, `bundle exec puma -t 8:16 -w 5 --preload -e production -d -b unix:///var/run/ucweb.sock --pidfile /var/run/ucweb.pid`
再SELECT count(*) FROM pg_stat_activity where client_addr= 'IP_of_WEB';(返回40, 即workers * min_threads = 5 * 8 = 40个).
页面卡的问题一下子就解决了, 数据库CPU也正常了.

* 任何一个查询都会占用1个CPU。慢的SQL会100%的占用CPU若干秒(用top查看)。所以CPU负荷大可能就是慢SQL过多。优化掉就OK了。

## 备份
如果有大表，备份费力，可以通过如下方式剔除
$ pg_dump -U postgres -Fc --exclude-table='big_table_name|not_important_big_table_name' your_production > your_production_20150728
## 还原
$ sudo -u lane pg_restore -d some_development < some_db
$ pg_restore -l some_production_0410 > 0401.list # 用这个-l可以看到这个pg_dump出来的文件有什么东西. 比如有哪些表, index等等. 方便你最后pg_restore完了比对下.
报错role "xxx" does not exist解决办法：
$ sudo -u postgres(or lane) createuser xxx

Restore From `xxx.sql` (https://gist.github.com/syafiqfaiz/5273cd41df6f08fdedeb96e12af70e3b)
```shell script
createdb db_name
$ $ psql -U <postgresql username> -d <db_name> -f <dump file that you want to restore like xxx.sql>
```

```bash
pg_dump -h yourproject-qa.fd2411323.us-east-1.rds.amazonaws.com -U postgres -f yourproject_qa_20210119.sql yourproject_qa # Backup from AWS. Need to wait for several minutes which depends on the size of your db. 
createdb yourproject_qa_20210119
psql -d yourproject_qa_20210119 -f yourproject_qa_20210119.sql # Restore from `.sql`
```

一个完整的数据库备份和还原的过程:
### 原数据库机器
```bash
$ cd /srv/database_backup
$ nohup /usr/local/pgsql/bin/pg_dump -U postgres -Fc some_production > some_production_0410 &
```

#### 新数据库机器
```bash
$ sudo -u postgres /usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data/ status
$ sudo -u postgres /usr/local/pgsql/bin/pg_ctl -D /usr/local/pgsql/data/ restart -m f # 带这两个参数才能正常的重启, 否则有client连接在是无法顺利关闭的
$ createdb -U postgres django_spa_blog_dev # For Mac brew installed PG
$ sudo -u postgres /usr/local/pgsql/bin/dropdb some_production # For linux
$ sudo -u postgres /usr/local/pgsql/bin/createdb some_production
$ cd /srv/db_35
$ scp root@172.35.11.21:/srv/database_backup/some_production_20160412 ./
$ nohup sudo -u postgres /usr/local/pgsql/bin/pg_restore -d some_production < /srv/db_35/some_production_0410 -v >> ./db_restore.log &
```

## 性能监控
### pgBadger
* 官方的包在CentOS上我发现无法解压
{% highlight bash %}
$ cd /root/pgbadger-master  # /home/soft/pgbadger-8.1
$ pgbadger --prefix 'postgresql.conf里面 log_line_prefix 的值(如'%t [%p]: [%l-1] ')' /path/to/your/pglog/*.log -o out.html
$ pgbadger --prefix '%t [%p]: [%l-1] user=%u,db=%d ' /pgdata95/pg_log/postgresql-Mon.log -o out_20160530.html
$ scp -P 22 root@173.130.1.132:/root/pgbadger-master/out_20160530.html ./ 
$ pgbadger --prefix '[%t/ %u/ %d/ %p]-' /root/pgbadger-master/logs_from_21/postgresql-Wed_1042.log -o out_20160413_1.html
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
查看当前的clients
$ SELECT usesysid, usename FROM pg_stat_activity;

### greenplum
http://greenplum.org/ 
查询性能成为问题的时候可以考虑用它

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
    $ 把取得的进程PID杀死，略等几秒后，pg应该会自动重生！

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

可以看日志或者查系统视图
你日志里应该设置记录duration
SELECT S.procpid, S.start, now() - start AS lap,S.current_query 
FROM (
     SELECT backendid,pg_stat_get_backend_pid(S.backendid) AS procpid,pg_stat_get_backend_activity_start(S.backendid) AS start, pg_stat_get_backend_activity(S.backendid) AS current_query 
     FROM (SELECT pg_stat_get_backend_idset() AS backendid) AS S 
) AS S ,pg_stat_activity a
WHERE S.procpid =a.pid
and a.state ='active'
ORDER BY lap DESC;

## AWS RDS
pg_dump and pg_restore
Refer to https://gist.github.com/syafiqfaiz/5273cd41df6f08fdedeb96e12af70e3b

```shell
pg_dump -h rds-server -d your_db_qa -U postgres -f your_db_qa_20220429.sql # backup
psql -U postgres -d your_db_qa_20220429 -f your_db_qa_20220429.sql # Restore
or compressed backup
pg_dump -h -d project_name_qa -U postgres -Fc -f project_name_qa_20220506 # compressed backup
createdb project_name_qa_20220506
pg_restore -d project_name_qa_20220506 < project_name_qa_20220506 # Restore
```
