---
title: Node更换npm源
date: 2018-03-17 22:43:02
tags: Node
categories: Frontend
keywords: 
- Node
- Npm
description: npm官方源速度太慢，更换cnpm源	
copyright: true
---

​	最近在做一些Node的应用，安装模块时官方的源不仅速度慢，很多模块无法下载（科学上网也不行）。没办法╮(╯_╰)╭，只好换成taobao源，整理了几种方法。

## 使用一次

```
//使用一次安装express服务
npm --registry https://registry.npm.taobao.org install express
```

## 永久替换

```
//可以先查看原来的源地址，做好备份
npm get registry

//将npm源直接替换
npm config set registry https://registry.npm.taobao.org

//换回官方源
npm config set registry https://registry.npmjs.org
```

## 同时使用npm、cnpm

```
//安装cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org

//之后可以使用cnpm安装服务，不影响npm
cnpm install express
```


