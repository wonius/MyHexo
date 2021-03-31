---
title: MacOS常用命令
date: 2017-08-15 00:23:31
tags: 
- MacOS
- Shell
categories: MacOS
keywords: Mac常用命令
description: 
copyright: true
---

## 查看端口号

```bash
//8080为端口号
lsof -i :8080	

//kill
sudo kill -9 pid
```

## 设置软件安装来源为所有来源

```bash
sudo spctl --master-disable/--master-enable
```

## redis

```bash
redis-server	//启动
```

## mysql

```bash
mysql.server start	//启动
```

## postgresql

```bash
pg_ctl -d /usr/local/var/postgres -l /usr/local/var/postgres/server.log start 	//启动
```

## 远程传递文件

```bash
scp **.zip username@**.**.**.**:/目录
```
