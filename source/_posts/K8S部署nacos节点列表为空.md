---
title: K8S部署Nacos节点列表为空
copyright: true
date: 2020-11-02 10:49:44
tags: K8S
categories: K8S
keywords:
- K8S
- Nacos
description: K8S部署Nacos节点列表为空，主机竞争失败
---

# 背景

​	最新因为项目需要，需要搭建一个配置中心，nacos相对来说教简单，而且还赠送一个注册中心，买一送一，业界良心啊，就它了。过程就不赘述了，下载`nacos-mysql`、`nacos-server`镜像，编写好helm模板后直接部署。

# 问题

## 发现

​	噼里啪啦一顿操作后，输入账号登录nacos-console，体验了一下，配置中心增删改查都正常，但是集群管理中的节点列表却是空的。

## 分析

1. 第一反应是查看服务的状态与日志，`kubectl log xx`和`kubectl describe po xx`看到都是成功的状态。

​	2. 先验证下配置中心吧，修改代码，加载配置中心的配置，成功。所以验证只有注册中心有问题。

​	3. 进入到nacos-server pod，查看日志。`/home/nacos/logs`目录下主要分2类日志，`config-*.log`和`naming-*.log`，分别对应配置中心的日志和注册中心的日志。

 4. 先看naming-raft.log，这个是选举日志，发现果然有问题。

    `WARN [IS LEADER] no leader is available now!`

 5. nacos在创建过程中，会将每个节点的地址存放在`/home/nacos/config/cluster.conf`，查看该文件，3个pod的内部域名也是正常。进到pod内部，telnet其余2台的域名、端口也是通的。

到这基本验证了pod创建是成功的，且相互之间也可以访问。问题应该出在3台pod在竞选时，没有使用cluster.conf中设置的内部域名，而是通过其他方式访问，导致相互之间访问失败。这时候目标转向service。

## 解决

然后到网上搜了下，有的说将service设置为`ClusterIP: none`，改为headless service。尝试了下，果然好了。

# 后续

​	为什么改为headless service就好了呢？

​	因为默认的Service的type是ClusterIP，会在创建service的时候，为service创建ip，所有访问该service的请求，都会经过该ip然后做负载均衡。如果设置为none，就不会为该service分配ip，只能通过pod的域名访问每个pod。一般pod间需要相互访问的服务都采用这种模式，比如zk等。

​	通过`kuectl get svc`可以查看所有的service。也可以通过`kubectl describe svc xxx`，查看每个service的详情。



​	

​	

