---
title: 搭建shadowsocks服务
copyright: true
date: 2018-05-06 13:53:55
tags: shadowsocks
categories: shadowsocks
keywords: shadowsocks
description: 服务器搭建shadowsocks服务
---

## 安装shadowsocks服务

### Centos

```
sudo yum install python-pip
sudo pip install shadowsocks
sudo pip install --upgrade pip
```

### Ubuntu

```
sudo -s
apt-get update
apt-get install python-pip
pip install shadowsocks
pip install --upgrade pip
```

## 配置文件

```
mkdir /etc/shadowsocks
vim /etc/shadowsocks/ss.json

//拷贝，别忘了改密码
{
    "server":"0.0.0.0",
    "server_port":443,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"XXXXXXX",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false,
    "workers": 1
}
```

server_port中配的端口，需要服务器上对外开放

## 启动

```
sudo /usr/local/bin/ssserver  -c /etc/shadowsocks/ss.json -d start
sudo /usr/local/bin/ssserver  -c /etc/shadowsocks/ss.json -d stop
sudo /usr/local/bin/ssserver  -c /etc/shadowsocks/ss.json -d restart

//设置开机自启
vim /etc/rc.local
//末尾添加
sudo /usr/local/bin/ssserver  -c /etc/shadowsocks/ss.json -d start
```

