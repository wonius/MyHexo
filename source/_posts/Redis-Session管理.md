---
title: Redis-Session管理
copyright: true
date: 2018-07-13 10:33:41
tags: Redis
categories: DB
keywords: Redis
description: 项目中使用redis存储session
---

# 背景

​	项目中使用redis存储session，写了个session切面用于维护session，当用户调用接口时，会先检验redis中是否有session（有效期为30min，调用接口刷新有效期），如果没有，创建session。在并发的场景下，需要用锁避免重复写。StringRedisTemplate中没有对锁的封装，所以自己简单封装了两个取锁的方法。

# tryLock

```java
    /**
     * 尝试获取锁，如果获取到，并设置过期时间
     * @param keyName
     * @param seconds
     * @return
     */
    public boolean tryLock(String keyName, Long seconds) {
        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
        boolean lockReturn = connection.setNX(keyName.getBytes(), LOCK_TRUE.getBytes());
        connection.close();
        if (lockReturn) {
            redisTemplate.expire(keyName, seconds, TimeUnit.SECONDS);
        }

        return lockReturn;
    }

```

# getLock

```java
    /**
     * 一定会获取到锁，并设置过期时间
     * @param keyName
     * @param seconds
     * @return
     * @throws InterruptedException
     */
    public boolean getLock(String keyName, Long seconds) throws InterruptedException {
        RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
        boolean lockReturn = connection.setNX(keyName.getBytes(), LOCK_TRUE.getBytes());
        while (!lockReturn) {
            Thread.sleep(10);
            lockReturn = connection.setNX(keyName.getBytes(), LOCK_TRUE.getBytes());
        }
        connection.expire(keyName.getBytes(), seconds);
        connection.close();
        return lockReturn;
    }
```

# releaseLock

```java
    /**
     * 释放锁
     * @param keyName
     * @return
     */
    public boolean releaseLock(String keyName) {
        return redisTemplate.delete(keyName);
    }
```

# 后记

在投产后，发现效果并不好，实例经常会跑着跑着，突然卡死。分析原因应该是redis连接池资源耗尽，导致服务器所有线程阻塞，整台实例不可用（一般跑2、3天才会出现）。因为项目已经使用了StringRedisTemplate，本身它会管理自己的连接池。而我通过getConnection获取到的连接，相当于脱离于连接池，有可能导致与StringRedisTemplate不兼容。

如果有同学自己通过jedis实现了jedispool进行管理，应该没有这个问题，感兴趣的小伙伴可以试一下。jedis无法自动关闭连接，当使用完之后，别忘了手动关闭。

后来，发现了Redisson，专门用于解决分布式问题，也提供了锁的实现，小伙伴可以用一下。

顺便再吐槽下StringRedisTemplate，感觉就是对redis的命令做了简单的封装，而且开启事务时还有坑...