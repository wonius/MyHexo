---
title: Jpa中使用@Scheduled报no transaction is in progress
copyright: true
date: 2020-01-13 10:11:38
tags: JPA
categories: Backend
keywords: 
- JPA
description: 在springboot+jpa中，使用@Scheduled时，报异常'no transaction in progress'
---

# 问题

环境：springboot+jpa

某个接口使用@Scheduled定时调度，内部方法中使用了jpa的saveandflush方法，方法执行时报异常'no transaction in progress'。

# 分析

1. 查看saveandflush实现，发现jpa内部实现都添加了事务@Trainsactional；
2. 不用@Scheduled注解时，直接调用方法正常；

所以基本确认通过@Scheduled创建的线程，没有继承上下文中的事务管理器。

# 解决

在@Scheduled注解的地方注入事务管理器。

https://stackoverflow.com/questions/33248846/spring4-scheduled-transaction-throws-no-transaction-is-in-progress-at-flush-fo

