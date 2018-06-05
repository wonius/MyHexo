---
title: Mysql迁移
date: 2018-01-24 11:05:46
tags: 
- Mysql
- 运维
categories: 
- DB
keywords: Mysql
description: 记录一次生产环境Mysql迁移记录
copyright: true
---

## 背景

​	生产环境上的Mysql服务器资源有限，需要迁移到另外一台资源更多的服务器。

## 准备工作

1. 新服务器安装mysql（如果本身有Mysql，重装Mysql，一定要重启服务;
2. 将mysql服务加入到开机自启列表;
3. 停服，备份数据;

## 开始

1. 目标服务器上新建Mysql用户、数据库；

2. 传输备份脚本；

   刚开始使用scp命令将备份脚本传输到目标服务器，使用Mysql命令恢复DB。备份脚本500多M，压缩后150多，采用这种方式实在是太慢。

   摸索其他方式，最后通过Mysql Workbench里的Schema Transfer Wizard功能进行同步，大大缩短了同步时间。

3. 应用连接Mysql进行验证，成功；

## 调优

​	迁移到新服务器之后，检测到应用响应时间过长，主要是因为数据库返回比较慢。对Mysql进行调优，主要是开启Mysql查询缓存，应用恢复正常响应速度。下面列举一些常用配置

1. 查询缓存的设置

   ```
   #查看查询缓存的设置
   #如下参数都可是使用 select @@global.XXXX; 单独查询
   show variables like '%query_cache%';

   hava_query_cache	查询缓存是否可用，YES/NO

   query_cache_limit	缓存大小，超过该值不会进行缓存，默认1M

   query_cache_min_res_unit 双刃剑，默认4KB，设置值大对大数据查询有好处，如果都是小数据查询，易造成内存碎片和浪费

   query_cache_size	缓存内存大小。
   				设置方法：set @@global.query_cache_size=1000000;
   				
   query_cache_type	查询缓存是否使用，OFF(0)/ON(1)/DEMAND(2)
   				当DEMAND时，必须使用SQL_CACHE关键字才会缓存，如select SQL_CACHE user_name from user;
   				当ON时，不想使用缓存使用sql_no_cache关键字，如select sql_no_cache user_name from user;另外，SQL中有用到mysql函数也不会缓存。
   				设置方法： set session query_cache_type=off;
   				
   query_cache_wlock_invalidate	

   #查看查询缓存的状态
   show status like '%Qcache%';
   Qcache_free_blocks	目前处于空闲状态的 Query Cache中内存Block数量
   Qcache_free_memory	目前处于空闲状态的 Query Cache内存总量
   Qcache_hits		Query Cache 命中次数
   Qcache_inserts		向 Query Cache中插入新的 Query Cache的次数，即没命中的次数
   Qcache_lowmem_prunes	当 Query Cache内存容量不走，删除老的 Query Cache给新对象的次数
   Qcache_not_cached	没有被Cache的SQL数，包含无法被Cache的SQL、由于query_cache_type设置不会被Cache的SQL
   Qcache_queries_in_cache	目前 Query Cache中的SQL数量
   Qcache_total_blocks	Query Cache中总Block数量

   #调优
   1. query_cache_min_res_unit的设置
   query_cache_min_res_unit = (query_cache_size - Qcache_free_memory)/ Qcache_queries_in_cache

   2. Qcache_Lowmem_prunes值较大，说明查询缓存区较小，需调大；

   3. Qcache_free_blocks 较多，说明内存碎片较多，需清理，FLUSH QUERY CACHE

   4. 查询缓存碎片率： Qcache_free_blocks / Qcache_total_blocks * 100%
   该值超过20%，使用FLUSH QUERY CACHE整理缓存碎片，或者调小query_cache_min_res_unit

   5. 查询缓存利用率：(query_cache_size - Qcache_free_memory) / query_cache_size * 100%
   该值在25%以下，说明query_cache_siz设置过大，可适当减小；
   该值在80%以上，且Qcache_lowmem_prunes > 50，说明query_cache_siz有点小，或者碎片太大；

   6. 查询缓存命中率：(Qcache_hits - Qcache_inserts) / Qcache_hits * 100%
   该值较低，说明写操作比较频繁，或者有些碎片
   ```

   ​

2. 其他配置

   大部分应用，设置查询缓存的配置就可以满足需要了，如果不够可以设置其他参数

   http://blog.csdn.net/nightelve/article/details/17393631

## one more thing

还有一点需要注意，有时设置了各项参数调整之后，Mysql反应还是很慢，这时候可以查看磁盘使用情况，有可能是磁盘容量不够，限制了读写导致查询效率低。
