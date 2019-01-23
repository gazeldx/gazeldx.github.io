---
layout: post
title:  "数据库优化"
date:   2016-04-13 14:15:31
categories: database
---

### 查询优化
* table index数量不宜过多。仅在查询比较多的column上创建index. string或text类型的字段创建索引效果比integer类型差很多。

* 如果通过table index过滤掉的数据少于90%，效率就不算高了。比如有5种color，color上建index，过多才过滤80%。

* 大表Join效率差。

* 字符串like的效率很差。

* 表太大，可以分拆成小表。如按企业ID分拆，按时间分拆等。要终合考虑实现难度和程序改动量。
按时间分拆可以根据业务需求，比如查询可以限制为仅允许查近三个月数据。历史数据查另外一个机器上的数据库。

* 用Redis等key-value数据库存放经常读写的数据，可以大大缓解Relational DB的压力。

### 其它
* RAID5硬盘安全性高,多个硬盘互相备份,一块坏了,另一块立刻可用.


