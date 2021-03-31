---
title: Mysql常见问题
date: 2018-01-30 22:21:12
tags: Mysql
categories: DB
keywords: 
- Mysql
description: Mysql常见问题
copyright: true
---

## ERROR 1044 (42000): Access denied for user ''@'localhost' to database 'XXX'

​	今天在DB里用普通用户做操作时，会报这个错误。主要是因为user表中有匿名用户，将匿名用户删除就可以了。

```bash
#首先进入mysql
mysql -uroot -p

#切换到mysql数据库
user mysql;
#查看当前用户
select host, user, password from user;
#删除匿名用户
delete from user where user='';
#写入
flush privileges;
#退出
\q

#重启服务
service mysqld restart
```

## ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)

​	一般由于密码错误，或者有用户没有密码的情况。	可以先设置成无密码模式进入，再将有问题的用户进行调整。

```bash
#修改my.cnf
vim /etc/my.cnf
#在[mysqld]中加入
skip-grant-tables

#保存退出后，重启mysql
service mysqld restart

#无需密码直接进入
mysql
#先看一下用户的状态，在判断要做什么操作
select user, password, host from mysql.user;
#删除不需要的用户
drop from mysql.user where password='';
#或者更新用户密码
update mysql.user set password=password("你的新密码") where user="XXX";
#写入
flush privileges;
#退出
\q

#重启服务
service mysqld restart
```

