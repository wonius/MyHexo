---
title: Spring注入static变量
copyright: true
date: 2018-07-26 09:29:49
tags: 
- Spring
categories: Spring
keywords: static
description: Spring注入static变量的两种方式。
---

# 使用setter方法

```
@Service("userService")
public class UserService implements IUserService {

    public static String hello;

    @Value("${hello.value}")
    public void setHello(String hello) {
        hello = hello;
    }

}
```

# 在spring容器中注入对象时赋值

```
@Service("userService")
public class UserService implements IUserService {

    public static String hello;

    @Value("${hello.value}")
    private String pHello;

    @PostConstruct
    public void init() {
        hello = pHello;
    }
    
}

```

