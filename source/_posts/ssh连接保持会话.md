---
title: SSH连接保持会话
date: 2018-02-03 14:02:23
tags: 
- Linux
- SSH
categories: 
- 运维
keywords: 
- Linux
- ssh
- macOS
description: SSH连接保持会话、设置别名
copyright: true
---

## 会话保持	

​	相信玩服务器的朋友都遇到过这个问题，终端里的ssh连接服务器经常超时，自动断开链接； 而又不想专门下载一个ssh客户端，一方面是因为安全问题，另一方面是觉得有些鸡肋。所以查了一下能否将ssh的会话保持住，不自动断开连接，还真有。

​	主要有两个参数：

```bash
#发送保持活跃的数据包时间间隔，单位为秒，默认300秒
ServerAliveInterval 60 
#系统ssh服务维持最大的保活连接数量，默认是3
ServerAliveCountMax 10 
```

​	如果需要更改全部用户的设置，修改/etc/ssh/ssh_config中的文件；

​	如果只改某一个用户中的设置，修改~/etc/ssh/ssh_config中的文件；

***PS:***

Host * :Host后面加的是服务器ip、ip端，*代表所有服务器

## 设置别名

​	为经常访问的服务器设置别名，就不用每次都写全信息了。同样，还是编辑ssh_config文件

```bash
//添加，后保存退出
Host prd
HostName 120.52.145.22
User log
IdentitiesOnly yes
IdentityFile  ~/.ssh/id_rsa.pub

//使用别名登录(如果不行重启下ssh服务:sudo service ssh reload)
ssh prd
```

其中，Host、HostName、User为必须，其他可以根据自己需要配置。各属性含义如下：

- Host 别名
- HostName 指定登录的主机名或IP地址
- Port 指定登录的端口号
- User 登录用户名
- IdentityFile 登录的公钥文件（如果是pem文件，可以将pem添加到ssh，ssh-add命令）
- IdentitiesOnly 只接受SSH key 登录
- PubkeyAuthentication
