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





<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品由<a xmlns:cc="http://creativecommons.org/ns#" href="http://wonius.top/" property="cc:attributionName" rel="cc:attributionURL">Gavin</a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。