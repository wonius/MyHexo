---
title: MacOS使用VS Code配置go环境
copyright: true
date: 2020-11-26 16:51:07
tags: 
- Go
- VS Code
categories: Go
keywords: go
description: MacOS使用VS Code配置go环境
---

# 安装Go

## 下载安装

访问[官网](https://golang.org/dl/)，根据需求下载。

我下载的是1.15.5，直接点击执行pkg文件安装就可以了。

默认是安装到`/usr/local/go`目录。

*PS: 也可以使用brew安装的方式*

## 设置环境变量

主要有2个环境变量：GOROOT、GOPATH。

GOROOT：go的安装路径

GOPATH：项目路径

根据情况修改配置文件。我用的是zsh，座椅在.zshrc最后追加如下

```bash
export GOROOT=/usr/local/go
export PATH=$PATH:$GOROOT/bin
export GOPATH=/Users/xxxx/go
export PATH=$PATH:$GOPATH/bin
```

改为别忘了source刷新下，然后`go version`验证下。

# VS Code配置

## 安装插件

在扩展商店里搜索go，安装就可以了。

## 修改配置

安装完成后，同时按住command+shift+p，输入`open settings`，打开setting.json，加入

`"go.gopath": "/Users/woniu/go"`

## 安装go组件

按住command+shift+p，输入`go: install/update tools`，然后勾选全部组件，确定，即可开始安装。

如果提示连接失败，可以改下代理

```bash
# shell中执行
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.io,direct
```

