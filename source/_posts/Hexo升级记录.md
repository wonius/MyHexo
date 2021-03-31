---
title: Hexo升级记录
copyright: true
date: 2020-10-19 15:58:36
tags: Hexo
categories: Hexo
keywords: Hexo
description: Hexo升级记录
---

# 背景

​	最近因为某些原因，升级了本机电脑上Node，然后引发了一连串的连锁反应，其中就包括Hexo。前后也花了点时间，mark下。

# 先升级Node

```shell
# 首先安装n组件
npm install -g n

# 使用n升级node版本。stable是最新的稳定版，latest是最新版。根据情况使用
sudo n stable
sudo n latest
# 查看版本验证
node -v

# 升级npm，xx为版本号。也可以使用latest，更新最新版本
sudo npm install npm@xx -g
# 验证npm版本
node -v 或者 node version

# 常用的一些命令
npm view node versions	// 查看node所有版本
npm view npm versions		// 查看npm所有版本
npm view npm version		// 查看npm最新版本
npm ls 或者 npm list		 // 查看npm所有插件，ls是list的简写
npm ls -g --dept 0		// 查看本机安装的npm插件
npm ls xx -g					// 查看xx相关的插件，比如hexo
```

# 再升级Hexo

```bash
# 安装npm-check、npm-upgrade
npm install -g npm-check
npm install -g npm-upgrade

# 检查更新
npm-check

# 更新package.json
npm-upgrade

# 升级。如果升级某个模块，在update后面加模块名xx，不加的话全部升级
npm update -g
# 保存package.json
npm update --save
```

如果是Mac OS，在升级过程中可能会遇到提示没有安装xcode组件的情况。

可以访问[苹果开发者下载中心](https://developer.apple.com/download/more/)，搜索`Command Line Tools`，根据系统版本找到对应的安装包，下载安装就可以了。

# 再修改主题

之前一直用的Next，时间久了有点审美疲劳，而且我当时的版本离最新版差别比较大，后续自己改过很多东西，升级也比较麻烦。所以干脆换了个主题。

发现Oranges挺清新脱俗的，就它了。

```bash
# 进入到hexo目录，安装主题
git clone https://github.com/zchengsite/hexo-theme-oranges.git themes/oranges

# 然后修改hexo的_config.yml中的主题
theme: oranges

# 重新编译、部署
hexo g && hexo d
```

# 后话

hexo升级后真不是一般的快哇，真香！