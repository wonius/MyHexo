---
title: CentOS彻底删除并重装、升级Mysql
date: 2017-12-18 22:05:18
tags: 
- Mysql
- CentOs
categories: DB
keywords: 
- CentOs
- Mysql
description: CentOS彻底删除并重装、升级Mysql
---

### 删除Mysql

```
//备份数据
mysqldump -u xxx -h xxx -P 3306 -p --all-databases > databases.sql
mysqldump -u xxx -h xxx -P 3306 -p *** > ***.sql

//停止服务
service mysqld stop

//卸载相关软件
yum remove mysql mysql-*

//找到遗留文件，并删除
find / -name mysql
sudo rm -rf **

//找到依赖包，并卸载
rpm -qa| grep mysql
yum remove ***
```



### 重装Mysql

```
//重新安装
yum install mysql mysql-server mysql-libs
```



### 升级高版本Mysql

```
//查看系统信息，el版本、i686还是x86——64等
uname -r

//到镜像中找适合自己的镜像，拿到链接
http://mirrors.sohu.com/mysql/

//下载镜像(适合我系统的镜像)
weget http://mirrors.sohu.com/mysql/MySQL-5.7/mysql-5.7.18-1.el6.x86_64.rpm-bundle.tar

//下载后解压
tar xvf mysql-5.7.18-1.el6.x86_64.rpm-bundle.tar

//依次安装服务
yum localinstall mysql-***-common-***.rpm mysql-***-libs-***.rpm mysql-***-client-***.rmp mysql-***-server-***.rpm

//查看mysql版本
mysql -V

//启动mysql
service mysqld start

//查看root用户初始密码
grep 'temporary password' /var/log/mysqld.log  (或/root/.mysql_secret)

//使用密码登录之后，用下面命令修改密码
ALTER USER ‘root'@'localhost' IDENTIFIED BY '你的密码'

//将Mysql加到服务自启动
chkconfig mysqld on
!8.6I)?9tf6;
```



<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品由<a xmlns:cc="http://creativecommons.org/ns#" href="http://wonius.top/" property="cc:attributionName" rel="cc:attributionURL">Gavin</a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。

&emsp;&emsp;
