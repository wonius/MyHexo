---
title: Mysql常用操作
date: 2017-07-30 21:03:22
tags: Mysql
categories: DB
keywords: 
- Mysql
description: Mysql常用操作
copyright: true
---

## MacOs，修改root用户密码

mysql版本：5.7.18

```
//先暂停服务
mysql.server stop

//启动安全模式
mysqld_safe --user=mysql --skip-grant-tables

//进入mysql
mysql -u root mysql

//设置密码
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('新密码');

//将变更刷入内存。该步骤我没有执行，也生效了
FLUSH PRIVILEGES; 

//退出
\q

//重新启动服务
mysql.server start
```
### 数据库备份、还原

1. 备份某库

   ```
   mysqldump -u用户名 -p密码 -h主机 数据库 a -w “sql条件” –lock-all-tables > 路径

   mysqldump -uroot -p1234 dbname a –where “tag='88′” –no-create-info> c:\a.sql
   mysqldump -u root -p game > /tmp/game_backup.sql
   ```


2. 备份某几个库

   ```
   mysqldump -u root -p game game2 > /tmp/game_backup.sql
   ```

3. 备份所有库

   ```
   mysqldump -u root -p --all-databases > /tmp/all_backup.sql
   ```

4. 还原数据

   ```
   mysql -u root -p game < /tmp/game_backup.sql
   ```

5. 跨主机同步

   ```
   #前提是host2主机上已经创建targetDb数据库, -C指示主机间的数据传输使用数据压缩
   mysqldump --host=host1 --opt sourceDb| mysql --host=host2 -C targetDb
   ```


## 创建用户

```
CREATE USER username IDENTIFIED BY 'password';

#限定在固定地址localhost登录
CREATE USER username@localhost IDENTIFIED BY 'password';

#当你在服务器主机上创建用户，为了方便远程连接，通常使用%代替，如：
CREATE USER username@'%' IDENTIFIED BY 'password';
```

## 授权

```
GRANT ALL PRIVILEGES ON *.* TO user@localhost IDENTIFIED BY 'password';

#授予某一些权限时
GRANT select,insert ON *.* TO test1@'%' IDENTIFIED BY 'password';
```

## 查看用户拥有的权限

```
show grants for user;
```

## 查看用户

```
select host, user from mysql.user;
```

## 创建库

```
CREATE DATABASE dbname;
```

## 删除库

```
DROP DATABASE dbname；
```
