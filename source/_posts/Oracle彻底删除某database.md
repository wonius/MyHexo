---
title: Oracle彻底删除某database
copyright: true
date: 2020-05-11 15:19:20
tags: Oracle
categories: DB
keywords: Oracle
description: Oracle彻底删除某database
---

环境：Oracle 12c

假设要删除的库名为hello，首先登陆上主机。

```sql
-- 在shell中执行下面命令，登陆sysdba用户
sqlplus / as sysdba;

-- 依次执行（该操作会将所有库删除）
shutdown immediate;
startup restrict mount;
drop database;
-- 然后退出sqlplus
quit;

-- 删除dbs下面的引导文件
cd $ORACLE_HOME/dbs
-- 将名称中包含库名（hello）的都删掉
rm -f hc_HELLO.dat
···

-- 查找并删除数据文件
find $ORACLE_BASE/ -name $ORACLE_SID
-- 将查找到文件都删除
rm -rf xxxx
```

