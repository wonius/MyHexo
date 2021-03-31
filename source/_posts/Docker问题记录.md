---
title: 容器内权限正常，无权限创建目录
copyright: true
date: 2019-12-30 10:15:55
tags: Docker
categories: Docker
keywords: Docker
description: 容器内权限正常，无权限创建目录
---

# 容器内权限正常，无权限创建目录

### 相关环境

1. Docker 18.09.07
2. kernel 4.4.185

### 问题

容器内用户为某特定用户，比如woniu，容器内应用要在a目录下创建新目录，a目录属主为woniu，权限750。但是在创建时报错，权限不足。

### 排查过程

1. 首先排查镜像是否正常。在其他主机run镜像，进入容器，可以成功创建目录；
2. 对比两个环境的docker版本、配置等，通过调整docker配置等，还是无法解决问题，排除docker影响；
3. 对比两个环境系统内核，发现内核版本不一致，分别为3.10.0 和4.4.185；

### 解决

将内核版本有4.4.185降为3.10.0，运行容器，正常。

### 总结

~~应该是docker在4.4.185上有bug，有待确认。~~

后来发现，貌似和tomcat版本有关，9.0版本普遍存在这个问题，跟镜像分层有关。

```dockerfile
FROM centos:7.2.1511
COPY apache-tomcat-9.0.30.tar.gz /opt/
# 之前的dockerfile，解压与授权通过两条RUN执行，在不同层级，权限有问题
RUN cd /opt \
&& tar xvf apache-tomcat-9.0.30.tar.gz \
RUN groupadd -g 5666 demo && useradd -l -r demo -g demo -u 666 \
&& chown -R demo:demo /opt/apache-tomcat-9.0.30

# 之后的dockerfile，解压与授权在一条RUN，同一层级，正常
RUN cd /opt \
&& tar xvf apache-tomcat-9.0.30.tar.gz \
&& groupadd -g 5666 demo && useradd -l -r demo -g demo -u 666 \
&& chown -R demo:demo /opt/apache-tomcat-9.0.30
```

https://github.com/docker-library/tomcat/issues/35

