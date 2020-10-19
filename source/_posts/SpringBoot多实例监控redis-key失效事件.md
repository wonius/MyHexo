---
title: SpringBoot多台实例监控redis-key失效事件
copyright: true
date: 2018-06-06 15:04:52
tags:
- SpringBoot
- Redis
categories:
- Spring
- SpringBoot
keywords:
- SpringBoot
- Redis
description: SpringBoot多台实例，监控redis中的变量失效事件，并做相关处理
---

# 背景

​	最近接到一个需求，redis中的变量失效时，需要记录到日志中。查了一下，可以通过订阅redis事件，java中进行处理。订阅事件会阻塞当前进程，所以需要开线程处理。后台服务是SpringBoot搭建，而且是多实例，为了确保每个事件仅消费一次，适用redis锁。

# Redis设置

​	redis中需要开启事件通知，默认是不开启的。

1. 修改redis.conf文件，设置notify-keyspace-events 为 Ex；
2. redis中执行`config set notify-keyspace-events Ex`（该方式重启redis失效）；

| 参数   | 作用                                   |
| ---- | ------------------------------------ |
| K    | 键空间通知，以`__keyspace@<db>__`为前缀        |
| E    | 键事件通知，以`__keysevent@<db>__`为前缀       |
| g    | del , expipre , rename 等类型无关的通用命令的通知 |
| $    | String命令                             |
| l    | List命令                               |
| s    | Set命令                                |
| h    | Hash命令                               |
| z    | 有序集合命令                               |
| x    | 过期时间（每次key过期时生成）                     |
| e    | 去住时间（当key在内存存满了被清除时生成）               |
| A    | g$lshzxe的别名，因此“AKE”意味着所有事件           |

# Java代码

## 设置JedisPool

### 增加jedis依赖

pom文件增加：

```
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

### yml中增加配置

```
jedis:
  max:
    total: 100
    idle: 10
    waitmillis: 10000
    timeout: 10000
  testOnBorrow: true
  host: localhost
  port: 6379
  password: 123456
```

### 实例化JedisPool

```java
package com.springboot.common;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/**
 * 请编写注释
 *
 * @author yangfan
 * @Date 2018/6/4
 */
@Configuration
@EnableCaching
public class JedisConfig extends CachingConfigurerSupport{
    @Value("${jedis.port}")
    private int port;
    @Value("${jedis.host}")
    private String host;
    @Value("${jedis.max.total}")
    private Integer maxTotal;
    @Value("${jedis.max.idle}")
    private Integer maxIdle;
    @Value("${jedis.max.waitmillis}")
    private Long maxWaitMillis;
    @Value("${jedis.max.timeout}")
    private int timeout;
    @Value("${jedis.password}")
    private String password;


    @Bean
    public JedisPool redisPoolFactory() {
        JedisPoolConfig jedisPoolConfig = new JedisPoolConfig();
        jedisPoolConfig.setMaxIdle(maxIdle);
        jedisPoolConfig.setMaxWaitMillis(maxWaitMillis);
        JedisPool jedisPool = new JedisPool(jedisPoolConfig, host, port, timeout, password);
        return jedisPool;
    }
}
```

## 创建监听类

```java
package com.springboot.common;

import com.springboot.util.ApplicationContextUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPubSub;

import javax.annotation.PostConstruct;

/**
 * 请编写注释
 *
 * @author yangfan
 * @Date 2018/6/4
 */
@Component
public class RedisListener extends JedisPubSub{

    private static final String INSTANCE_LOCK = "INSTANCE_LOCK";

    @Autowired
    private JedisPool jedisPool;
    Jedis jedis = null;

    @Override
    public void onPMessage(String pattern, String channel, String message) {

		// 线程中无法通过注解获取注入对象，在ApplicationContext中获取
        jedisPool = ApplicationContextUtil.getBean(JedisPool.class);
        jedis = jedisPool.getResource();

	    // 加锁。setnx是原子性操作，设置成功返回1，否则返回0
        Long lock = jedis.setnx(INSTANCE_LOCK, "true");
        if (lock == 0) {
        	jedis.close();
            return ;
        }

	    // 写处理逻辑，可以做其他操作，也可以入库
        System.out.println(pattern + "=" + channel + "=" + message);
        
        jedis.del(INSTANCE_LOCK);
        jedis.close();
    }

    @PostConstruct
    void init() {
    	// 订阅事件，使用线程中操作，否则阻塞当前进程
        SubThread subThread = new SubThread(jedisPool);
        Thread thread = new Thread(subThread);
        thread.start();
    }
}
```

一开始的方案，实例共用一个锁，实际发现可能会有实例A处理完，实例B才开始处理，导致处理多次的情况。

然后优化为将锁时间加长的方案。

而如果将锁时间加长，会导致多台实例处理是阻塞，进而优化为每个Key加锁、并延长锁时间的方案。

修改后如下：

```java
    @Override
    public void onPMessage(String pattern, String channel, String message) {

        if (StringUtils.startsWith(message, KEYS_LOCK)) {
            return;
        }

        jedisPool = ApplicationContextUtil.getBean(JedisPool.class);
        jedis = jedisPool.getResource();

        Long lock = jedis.setnx(KEYS_LOCK + message, "true");
        jedis.expire(KEYS_LOCK + message, 5);
        if (lock == 0) {
            jedis.close();
            return ;
        }

        System.out.println(pattern + "=" + channel + "=" + message);
        jedis.del(KEYS_LOCK);
        jedis.close();
    }
```

## 创建线程类

```java
package com.springboot.common;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;

/**
 * 请编写注释
 *
 * @author yangfan
 * @Date 2018/6/4
 */
public class SubThread implements Runnable {

    private JedisPool jedisPool;
//	经测试，仅能通过 * 正则匹配，如果加前缀，匹配不到
    String pattern = "*";
//    String pattern0 = "keyspace@0:SessionID_*";
//    String pattern1 = "__key*__:*";

//	可以监听某一特定channel的事件
//    String pattern2 = "__keyevent@0__:*";

//	入参可以为String数组
//    String[] patterns = new String[]{"SessionID_*"};


    public SubThread(JedisPool jedisPool) {
        this.jedisPool = jedisPool;
    }

    @Override
    public void run() {
    	//如果线程中出现问题，线程直接挂掉。
    	//使用while，重启线程
        while(true) {
            Jedis jedis = jedisPool.getResource();
            try {
                jedis.psubscribe(new RedisListener(), pattern);
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                jedis.close();
            }
        }
    }
}

```

## 创建ApplicationContextUtil类

```java
package com.springboot.util;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

/**
 * 请编写注释
 *
 * @author yangfan
 * @Date 2018/6/6
 */
@Component
public class ApplicationContextUtil implements ApplicationContextAware {

    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        if (ApplicationContextUtil.applicationContext == null) {
            ApplicationContextUtil.applicationContext = applicationContext;
        }
    }

    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    public static Object getBean(String name) {
        return ApplicationContextUtil.getApplicationContext().getBean(name);
    }

    public static <T>T getBean(Class<T> clazz) {
        return ApplicationContextUtil.getApplicationContext().getBean(clazz);
    }

    public static <T>T getBean(String name, Class<T> clazz) {
        return ApplicationContextUtil.getApplicationContext().getBean(name, clazz);
    }
}
```

