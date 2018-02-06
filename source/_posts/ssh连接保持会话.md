---
title: ssh连接保持会话
date: 2018-02-03 14:02:23
tags: 
- Linux
- ssh
- macOS
categories: 
- 运维
keywords: 
- Linux
- ssh
- macOS
description: ssh连接保持会话，不自动中断
---

​	相信玩服务器的朋友都遇到过这个问题，终端里的ssh连接服务器经常超时，自动断开链接； 而又不想专门下载一个ssh客户端，一方面是因为安全问题，另一方面是觉得有些鸡肋。所以查了一下能否将ssh的会话保持住，不自动断开连接，还真有。

​	主要有两个参数：

```
#发送保持活跃的数据包时间间隔，单位为秒，默认300秒
ServerAliveInterval 60 
#系统ssh服务维持最大的保活连接数量，默认是3
ServerAliveCountMax 10 
```

​	如果需要更改全部用户的设置，修改/etc/ssh/ssh_config中的文件；

​	如果只改某一个用户中的设置，修改~/etc/ssh/ssh_config中的文件；

***PS:***

Host * :Host后面加的是服务器ip、ip端，*代表所有服务器

