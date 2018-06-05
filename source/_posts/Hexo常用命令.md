---
title: Hexo相关配置
date: 2017-12-18 22:09:22
tags: Hexo
categories: Hexo
keywords: Hexo
description: Hexo常用命令
copyright: true
---

## 创建新文档

```shell
hexo new filename
```

## 启动本地服务

```
#启动本地服务，然后访问http://localhost:4000/
hexo s
```

## 重新编译并且发布

```
hexo g && hexo d
```

## 清理缓存

```
hexo clean
```

## 部署到vps

一般使用Hexo搭建blog，都是采用Hexo+Github的结构，但是由于Github屏蔽了百度的爬虫，所以建好的blog百度搜不到，满足不了知识分享（装逼）的需求。解决办法有很多，最简单的就是买一块vps，将blog直接推送到自己的vps上。

```
//安装hexo-deployer-rsync
npm install hexo-deployer-rsync --save

//修改Hexo中的_confg.xml的deploy
- type: git
  repo:
      github: https://github.com/XXXX/xxxx.github.io.git 
      coding: https://git.coding.net/XXXX/xxxx.git
  branch: master
- type: rsync
  host: 140.143.251.238
  user: root
  root: /data/www/hexo
  port: 22
  delete: true
  verbose: true
  ignore_errors: false
  
//生成并部署
hexo g && hexo d
```

参数讲解：

| 参数                | 描述          | 默认值  |
| ----------------- | ----------- | ---- |
| **host**          | 远程主机的地址     |      |
| **user**          | 使用者名称       |      |
| **root**          | 远程主机的根目录    |      |
| **port**          | 端口          | 22   |
| **delete**        | 删除远程主机上的旧文件 | true |
| **verbose**       | 显示调试信息      | true |
| **ignore_errors** | 忽略错误        | true |
