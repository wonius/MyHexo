---
title: MacOS搭建Aria2
copyright: true
date: 2018-03-30 14:09:16
tags: 
- MacOS
- Aria2
categories: MacOS
keywords: Aria2
description: MacOS搭建Aria2
---

## 安装Aria2

### 安装

MacOs安装Aria2十分简单，使用homebrew安装就可以

```
brew install aria2
```

### 配置

```
//创建配置目录
mkdir ~/.aria2

//编辑配置文件
vim ~/.aria2/aria2.conf

//将下面配置拷贝到aria2.conf中，并且修改dir为自己的下载目录
#用户名
#rpc-user=user
#密码
#rpc-passwd=passwd
#上面的认证方式不建议使用,建议使用下面的token方式
#设置加密的密钥
#rpc-secret=token
#允许rpc
enable-rpc=true
#允许所有来源, web界面跨域权限需要
rpc-allow-origin-all=true
#允许外部访问，false的话只监听本地端口
rpc-listen-all=true
#RPC端口, 仅当默认端口被占用时修改
#rpc-listen-port=6800
#最大同时下载数(任务数), 路由建议值: 3
max-concurrent-downloads=5
#断点续传
continue=true
#同服务器连接数
max-connection-per-server=5
#最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要
min-split-size=10M
#单文件最大线程数, 路由建议值: 5
split=10
#下载速度限制
max-overall-download-limit=0
#单文件速度限制
max-download-limit=0
#上传速度限制
max-overall-upload-limit=0
#单文件速度限制
max-upload-limit=0
#断开速度过慢的连接
#lowest-speed-limit=0
#验证用，需要1.16.1之后的release版本
#referer=*
#文件保存路径, 默认为当前启动位置
dir=/Users/woniu/Downloads
#文件缓存, 使用内置的文件缓存, 如果你不相信Linux内核文件缓存和磁盘内置缓存时使用, 需要1.16及以上版本
#disk-cache=0
#另一种Linux文件缓存方式, 使用前确保您使用的内核支持此选项, 需要1.15及以上版本(?)
#enable-mmap=true
#文件预分配, 能有效降低文件碎片, 提高磁盘性能. 缺点是预分配时间较长
#所需时间 none < falloc ? trunc « prealloc, falloc和trunc需要文件系统和内核支持
file-allocation=prealloc
```

### 运行aria2

```
aria2c

//检查是否已启动
ps aux|grep aria2c
```

## UI设置

访问https://ziahamza.github.io/webui-aria2/

然后点击设置-链接设置

将主机修改为：localhost

端口：6800

取消ssl/ssh的勾选

## 安装百度云插件

下载[百度插件](https://yalv.me/download/aria2/Exporter.zip)，解压

在chrome浏览器中，设置-更多工具-扩展程序-开启开发者模式-加载已解压的拓展程序

安装之后，随便打开一个百度网盘的资源，发现多了一个导出下载的选项，里面有ARIA2 RPC，使用这个下载rpc文件，然后自动会使用aria2进行下载

## ~~安装迅雷插件（迅雷已屏蔽）~~

该方案目前已经被迅雷屏蔽