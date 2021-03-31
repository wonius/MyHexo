---
title: NodeJs安装与升级
copyright: true
date: 2020-10-14 17:25:24
tags: NodeJs
categories: Frontend
keywords: NodeJs
description: NodeJs安装与升级
---

# NodeJs安装

首先安装homebrew

```bash
# 使用brew安装node
brew install node

# 然后查看版本
node -v
npm -v
```

# NodeJs升级

```bash
# 清楚node的缓存
sudo npm cache clean -f

# 安装n
sudo npm install -g n

# 用n安装最新的稳定版本
sudo n stable

# 升级npm
sudo npm install npm -g

# 查看版本
node -v
npm -v
```

其他命令

```bash
# node所有版本
npm view node versions 
# npm所有版本
npm view npm versions 
# 已安装插件
npm list
```

