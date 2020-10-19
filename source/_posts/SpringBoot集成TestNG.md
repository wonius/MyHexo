---
title: SpringBoot集成TestNG
copyright: true
date: 2018-04-27 11:21:06
tags: 
- SpringBoot
- TestNG
categories: 
- Spring
- SpringBoot
keywords: 
- SpringBoot
- TestNG
description: SpringBoot集成TestNG
---

## 增加dependency

```
<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng</artifactId>
    <version>6.1.1</version>
    <scope>test</scope>
</dependency>
```

## 配置maven-surefire-plugin

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <configuration>
        <suiteXmlFiles>
            <suiteXmlFile>${basedir}/src/test/resources/MyTestNG.xml</suiteXmlFile>
        </suiteXmlFiles>
        <!-- 不单独开线程启动test -->
        <forkMode>never</forkMode>
    </configuration>
</plugin>
```

## 增加MyTestNG.xml

```
<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd" >
<suite name="Suite">
    <test name="test1" verbose="2">
        <classes>
            <class name="com.springboot.testng.FirstTest">
                <methods>
                    <include name="test"></include>
                </methods>
            </class>
        </classes>
    </test>
    <test name="test2" verbose="2">
        <groups>
            <run>
                <include name="testGroup"></include>
            </run>
        </groups>
    </test>
</suite>
```

增加对应的类

## 注解

常用注解就那么几个，可以见下面的文章

https://blog.csdn.net/dragonpeng2008/article/details/70820902