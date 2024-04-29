
---
layout: post
title:  "Rails PostgreSQL Partitioning"
date:   2016-07-14 14:40:24
categories: Ruby
---

https://www.postgresql.org/docs/9.5/static/ddl-inherit.html 表继承

https://www.postgresql.org/docs/9.5/static/ddl-partitioning.html 分表技术

https://github.com/fiksu/partitioned Rails中实现PostgreSQL分表技术的gem

https://www.postgresql.org/docs/9.1/static/ddl-schemas.html Schema

用PostgreSQL的表继承, 主表customers只定义表的结构, 不存储任何数据。分表继承主表customers, 一个分表只存储该企业的customers。分表命名为: p60382。

分表在schema employees_partitions中, 其它PostgreSQL表在public schema中, 更方便管理, 不会出现表太多使得用pgadmin等工具不方便找表的问题。

Ensure that the constraint_exclusion configuration parameter is not disabled in postgresql.conf. If it is, queries will not be optimized as desired.

```sql
Customer.create_infrastructure
   (0.8ms)  select count(*) from pg_namespace where nspname = 'customers_partitions'
   (14.6ms)  CREATE SCHEMA customers_partitions
   (23.2ms)            CREATE OR REPLACE FUNCTION always_fail_on_insert(table_name text) RETURNS boolean
              LANGUAGE plpgsql
              AS $$
           BEGIN
             RAISE EXCEPTION 'partitioned table "%" does not support direct inserts, you should be inserting directly into child tables', table_name;
             RETURN false;
           END;
          $$;

   (5.8ms)            CREATE OR REPLACE RULE customers_insert_redirector AS
            ON INSERT TO customers
            DO INSTEAD
            (
               SELECT always_fail_on_insert('customers')
            )
```
