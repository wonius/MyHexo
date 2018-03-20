---
title: Mysql重装后无法正常启动
date: 2018-01-17 22:23:19
tags: Mysql
categories: DB
keywords: mysql.sock
description: 在服务器上重新安装了Mysql，使用mysql命令一直报错"ERROR 2002 (HY000) Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)"。使用service mysqld start命令启动服务也一直失败
---

## 背景

​	最近在做服务器上的DB迁移，服务器上的Mysql重新安装后无法正常启动服务，查看版本信息正常。

​	当使用mysql -uroot -p命令登录时，报

```
ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)
```



​	怀疑服务没有启动，启动服务时service mysqld start，报

```
MySQL Daemon failed to start.

Starting mysqld:   [FAILED]
```

## 处理

​	百度了一下，有的说没有权限，需要授权，有的说需要在/etc/my.cnf中加配置。试过都不行。最后尝试将mysql所有文件删掉，杀掉所有进程重启，终于成功。

```
//删除文件、自启服务、杀掉进程
rm -rf /var/lib/mysql/
rm /var/lock/subsys/mysqld
killall mysqld

//启动服务
service mysqld start
```

## one more thing

​	除了以上情况出现“ERROR 2002 (HY000) Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock' (2)”报错，还有另外一种情况，已经安装好mysql服务，然后将mysql移动到另外的目录，启动mysql时找不到mysql.sock，是因为my.cnf中配置的路径还是原来的目录，将my.cnf中的配置修改就可以了

```
#编辑my.cnf，事先可以使用cp命令备份
vim /etc/my.cnf

#修改新的目录，我的是/data1/mysql
[mysqld]
datadir=/data1/mysql
socket=/data1/mysql/mysql.sock

[mysql]
socket=/data1/mysql/mysql.sock
```





<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品由<a xmlns:cc="http://creativecommons.org/ns#" href="http://wonius.top/" property="cc:attributionName" rel="cc:attributionURL">Gavin</a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。