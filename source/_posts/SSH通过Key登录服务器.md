---
title: SSH通过Key登录服务器
date: 2018-03-18 20:40:52
tags: 
- Linux
- SSH
categories: 
- 运维
keywords: 
- Linux
- SSH
description: SSH通过Key登录服务器
copyright: true
---

## 服务器上创建访问用户

该步为可选，根据个人需要创建。不推荐直接使用root用户。

```
//创建用户
adduser gavin

//设置密码
passwd gavin

//增加sudo权限
vim /etc/sudoers
//添加下行
gavin  ALL=(ALL)       ALL
```

通常为了增加密码复杂度，我会用[在线生成密码工具](http://tool.c7sky.com/password/) 生成密码

## 本机创建秘钥/公钥对（Key）

网上很多帖子都直接用**ssh-keygen**，或者**ssh-keygen -t rsa**，这样会生成名为id_rsa的Key（默认名），这样当你每次需要生成新的Key时，会覆盖以前的。如果你的所有认证共用一组Key，也可以，不过不推荐。

下为用rsa算法，在~/.ssh/下生成名为id_rsa.qcloud，注释为qcloud的Key：

```
//qcloud对应腾讯云，可以根据自己情况取名
ssh-keygen -t rsa -f ~/.ssh/id_rsa.qcloud -C "qcloud"
```

然后一路Enter（可以选择输入passphrase）

```
cd ~/.ssh/
ll
```

查看生成的**id_rsa.qcloud**、**id_rsa.qcloud.pub**

## 将公钥拷贝到服务器

私钥存本地，公钥放到服务器

```
//用gavin用户登录服务器，创建.ssh目录
mkdir .ssh

//本机拷贝到服务器
scp -r ~/.ssh/id_rsa.qcloud.pub gavin@XXX.XXX.XXX.XXX:/home/gavin/.ssh/authorized_keys

//修改服务器上.ssh、authorized_keys权限，仅当前用户可操作
chmod 700 .ssh
chmod 600 .ssh/authorized_keys
```

## 服务器开启秘钥验证

```
vim /etc/ssh/sshd_config

//开启秘钥验证
RSAAuthentication yes
PubkeyAuthentication yes

//另外可以关闭密码验证
PasswordAuthentication no

//重启服务
service sshd restart
```

## 本机设置别名

前面设置完，就可以无密码登录了。不过每次都要输入服务器ip，比较反人类。可以进一步设置别名，使体验更友好。

```
vim /etc/ssh/ssh_config (或者~/.ssh/ssh_config)

//添加，保存退出
Host qcloud
HostName XXX.XXX.XXX.XXX
User gavin
IdentitiesOnly yes
IdentityFile  ~/.ssh/id_rsa.pub
```

如此设置之后，就可以直接**ssh qcloud**登录，方便快捷。