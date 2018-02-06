---
title: CentOS安装NodeJS
date: 2018-02-06 11:09:48
tags: 
- CentOS
- NodeJS
categories: NodeJS
keywords: 
- NodeJS
- CentOS
description: CentOS安装NodeJS方法
---

​	最近需要在服务器上安装nodejs，网上搜了一下，文章都是转来转去，都出到9.x版本了还在讲如何装6.x，很无语。所以自己整理一下。

### 选择合适的版本

​	yum安装自带nodjs版本太低，到[node源码版本](https://github.com/nodesource/distributions)上选择合适的版本，我安装了最新版本，9.x

```
curl -sL https://rpm.nodesource.com/setup_9.x | bash -
```

### 安装

```
yum install -y nodejs
```

### 检查node、npm版本

```
node -v
npm -v
```

### one more thing

​	除了在线安装，还有下载安装包、加压、编译安装的方法，操作繁琐，不推荐。