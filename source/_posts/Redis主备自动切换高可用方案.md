---
title: Redis主备自动切换高可用方案
copyright: true
date: 2020-10-14 09:45:03
tags: Redis
categories: DB
keywords: Redis
description: 2台Redis搭建主备高可用，并且主备可以自动切换
---

# 背景

​	某个客户购买了我们产品，需要到客户机房搭建高可用的redis，而且要求出现故障能够自动切换主备。考虑到节省机器，而且redis中数据也不会超过1G，决定用主从架构+keepalived实现。

# 搭建

## 所需资源

1. 2台主机
2. 1个vip

## redis主从搭建

1. 2台主机分别安装reids，并设置为开机自启动
2. 修改redis.conf，设置主从

## 设置keepalived

1. 2台主机分别安装keepalived，并设置为开机自启动
2. 编写监听redis的脚本、修改master状态的脚本、修改slave状态的脚本
3. 修改keepalived.conf，设置vip、各脚本

## 验证

1. 先查看2台redis（A、B）的角色
2. 手动将master（A）的redis关闭，然后查看slave（B）的角色是不是切换为master
3. 在新的master（B）中设置一个值，比如hello
4. 重新启动A，查看A的角色是否为slave，并验证A中是否将hello同步过来

