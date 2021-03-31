---
title: CentOS定时服务、开启自启
date: 2018-01-06 20:54:56
tags: 
- Linux
- 运维
- Shell
categories: 
- Linux
keywords: 
- Linux
- 定时任务
- chkconfig
description: CentOS定时服务、开启自启
copyright: true
---

## 加入定时任务

```bash
#创建shell脚本
vim bakup.sh

#赋予执行权限
chmod -R 777 bakup.sh

#加入定时任务
vim /etc/crontab
#最后一行加入。每天凌晨1点执行
0 1 * * * root /usr/bakup.sh

#重启crontab服务
service crond restart

#常用cron表达式
    每五分钟执行  */5 * * * *

　　每小时执行     0 * * * *

　　每天执行       0 0 * * *

　　每周执行       0 0 * * 0

　　每月执行       0 0 1 * *

　　每年执行       0 0 1 1 *
```

## 加入自启动服务

主要有两种方法，chkconfig命令和修改rc.local文件

1. chkconfig命令

   ```bash
   //首先查看要启动的服务是否在chkconfig列表中
   chkconfig --list

   //如果列表中没有，需要单服务添加到列表
   chkconfig --add XX

   //打开自启动
   chkconfig mysqld on

   //取消自启动
   chkconfig mysqld off

   ps:2~5为on，就是表明打开自启动开关了
   ```

2. 修改/etc/rc.d/rc.local

   ```bash
   //编辑rc.local
   vim /etc/rc.d/rc.local

   //添加服务
   /etc/rc.d/init.d/mysqld start
   ```

   
