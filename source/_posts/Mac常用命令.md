---
title: Mac常用命令
date: 2017-08-15 00:23:31
tags: 命令
categories: MAC
keywords: Mac常用命令
description: 
---

## 查看端口号

```
lsof -i tcp:port	//port为端口号
```

## 设置软件安装来源为所有来源

```
sudo spctl --master-disable/--master-enable
```

## redis

```
redis-server	//启动

```

## mysql

```
mysql.server start	//启动
```

## postgresql

```
pg_ctl -d /usr/local/var/postgres -l /usr/local/var/postgres/server.log start 	//启动
```

## 远程传递文件

```
scp **.zip username@**.**.**.**:/目录
```

