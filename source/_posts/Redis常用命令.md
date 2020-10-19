---
title: Redis常用命令
copyright: true
date: 2018-08-30 09:51:16
tags: Redis
categories: DB
keywords: Redis
description: Redis常用的一些命令、配置
---

# client list

查看连接的客户端

# monitor

实时监控redis执行的命令

# info

redis信息

# 配置密码

找到对应的redis.conf文件，一般在/etc/目录下，然后找到requirepass，修改后面的值，并将前面的# 删掉

```
# requirepass foobared
```

# pub/sub

publish：发布；

subscribe：监听某一频道；

psubscribe：支持正则；