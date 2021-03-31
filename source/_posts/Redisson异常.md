---
title: Redisson遇到的坑
copyright: true
date: 2018-08-30 09:13:11
tags: Redis
categories: DB
keywords: Redisson
description: Redisson使用过程中遇到的一些坑
---

# WRONGTYPE Operation against a key holding the wrong kind of value

 ```java
RLock lc = redisson.getLock("LOCK"); 
lc.lock(1000,TimeUnit.MILLISECONDS);
 ```

## 原因：

redisson中的getLock为了保持原子性，使用Lua脚本一次提交。在设置锁时，使用hset。

当redis里面已经存在"LOCK"的变量，调用getLock（内部实现为hset("LOCK")），会报该异常。

## 处理：

先确保redis中的"LOCK"参数删掉，在调用lock();

# unlock方法中的publish

```java
RLock lc = redisson.getLock("LOCK");
lc.unlock();
```

## 背景

项目中使用`psubscribe "*"`订阅了所有频道，在onMessage()监听处理中用到了Redisson锁，并且在函数最后有unlock()释放锁操作。

## 现象

代码运行时，发现会一直触发onMessage()，造成了死循环。

## 分析

使用monitor实时监控redis执行的命令，发现在unlock时，会依次做下面的操作：

```bash
exists XXXX
hexists XXXX ******
hincrby XXXX ****** -1
del XXXX
publish redisson_lock_channel:{XXXX} 0
```

可以看到在最后会向redisson_lock_channel:{XXXX} publish 一个0

## 处理

在onMessage()中针对自己想订阅的频道处理，否则return;