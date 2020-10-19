---
title: SpringRedisData中事务的坑
copyright: true
date: 2018-09-07 09:38:39
tags: Spring
categories:
- Spring
keywords: 
- RedisTemplate
- 事务
description: 主要是SpringRedisData 1.x版本中事务相关的坑
---

## 背景

​	项目中使用redis存储会话，考虑到并发场景，在对session进行管理的时候需要加锁。我们项目中使用了StringRedisTemplate，故而打算用它实现一个分布式锁。

## 过程

### version 1

​	首先，使用setnx达到锁的效果，为了防止系统异常出现死锁，还需要为每个锁设置过期时间。由于这些都是非原子性操作，所以使用redis事务进行处理。

```java
public void getLock(String key) {

  stringRedisTemplate.multi();
  stringRedisTemplate.opsForValue().set("SessionLock", "true");
  stringRedisTemplate.exec();
}
```

​	然而发现事务并没起作用，后来查看源码发现，如果没有是使用`@Transactional`注解，stringRedisTemplate的每个操作都会新建一个连接，不同连接没有实现multi的特性。

### version 2

​	发现问题继而加上注解



https://www.ibm.com/developerworks/cn/java/j-master-spring-transactional-use/index.html

https://blog.csdn.net/weixin_38399962/article/details/82753763

https://juejin.im/post/5ce5638ef265da1b91636a56

https://www.jianshu.com/p/d72e8526bea1

https://www.cnblogs.com/toutou/p/redis_lock.html

https://blog.csdn.net/liuhanfanshuang/article/details/52136438

https://blog.csdn.net/qq_34021712/article/details/79606551?depth_1-utm_source=distribute.pc_relevant_right.none-task&utm_source=distribute.pc_relevant_right.none-task

https://blog.csdn.net/congcong68/article/details/52734735