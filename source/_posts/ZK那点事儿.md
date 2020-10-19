---
title: ZK那点事儿
copyright: true
date: 2020-03-25 11:16:12
tags: ZooKeeper
categories: 中间件
keywords: ZooKeeper
description: ZK相关的一些知识点
---

## 特性

1. 应用场景：集群管理、注册发现中心、分布式锁；
2. 集群为单数，是为了省资源；
3. 最小集群实例数为3台。如果只有2台，一台都不能挂，跟单点效果是一样的。
4. 加入新节点需要修改所有的zoo.cfg；
5. 修改完后如果生效需要重启。有两种方案：1. 同时重启；2.     按特定顺序重启；
6. 如果先重启leader，会造成脑裂；
1. Port1：非选举通讯端口；port2：选举通讯端口；
2. 选举规则：先看事务id，相同，在看server.id；选票（事务id，server.id）；
3. 集群越大，节点越多，针对每个节点的通讯消耗越大，socket线程越多；
4. 每台实例一个Znode，容量1M，数据存储在内存中；
5. zk是cp架构，不适合做注册中心，注册中心更适合ap架构，nacos就是；