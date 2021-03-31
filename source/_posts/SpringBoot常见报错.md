---
title: SpringBoot常见报错
copyright: true
date: 2018-04-18 14:50:55
tags: SpringBoot
categories: 
- Spring
- SpringBoot
keywords: SpringBoot
description: SpringBoot常见报错
---

## Whitelabel Error Page

这种情况一般是配置不对，仔细检查下。

另外，新手常犯的错误就是，使用`@SpringBootApplication`注解的Application类，需要与controller同级目录。因为默认从Application类当前目录向下的子目录里扫描。

## Detected both xxx.jar and bound xxx.jar on the class path

jar包冲突，根据情况删除其中一个jar包。

由于springboot中很多包打包在一起，下面教大家如何看项目中的包分别包含哪些包：

1、使用idea中的`Show Dependencies`

打开`View - Tool Windows - Maven Projects`

{% asset_img idea_maven_jar.png %}

点击后，生成包依赖视图

{% asset_img maven_jar.png %}

2、 如果你的idea没有这个功能，可以使用maven命令显示结构，是一个树状图

```bash
mvn dependency:tree

//生成结构如下：
[INFO] com.springboot:myspringbootdemo:jar:0.0.1-SNAPSHOT
[INFO] +- org.springframework.boot:spring-boot-starter-jdbc:jar:2.0.1.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter:jar:2.0.1.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot:jar:2.0.1.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-autoconfigure:jar:2.0.1.RELEASE:compile
[INFO] |  |  +- org.springframework.boot:spring-boot-starter-logging:jar:2.0.1.RELEASE:compile
[INFO] |  |  |  +- ch.qos.logback:logback-classic:jar:1.2.3:compile
[INFO] |  |  |  |  \- ch.qos.logback:logback-core:jar:1.2.3:compile
[INFO] |  |  |  +- org.apache.logging.log4j:log4j-to-slf4j:jar:2.10.0:compile
[INFO] |  |  |  |  \- org.apache.logging.log4j:log4j-api:jar:2.10.0:compile
[INFO] |  |  |  \- org.slf4j:jul-to-slf4j:jar:1.7.25:compile
[INFO] |  |  +- javax.annotation:javax.annotation-api:jar:1.3.2:compile
[INFO] |  |  \- org.yaml:snakeyaml:jar:1.19:runtime
[INFO] |  +- com.zaxxer:HikariCP:jar:2.7.8:compile
[INFO] |  \- org.springframework:spring-jdbc:jar:5.0.5.RELEASE:compile
[INFO] |     +- org.springframework:spring-beans:jar:5.0.5.RELEASE:compile
[INFO] |     \- org.springframework:spring-tx:jar:5.0.5.RELEASE:compile
[INFO] +- org.springframework.boot:spring-boot-starter-web:jar:2.0.1.RELEASE:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-json:jar:2.0.1.RELEASE:compile
[INFO] |  |  +- com.fasterxml.jackson.core:jackson-databind:jar:2.9.5:compile
[INFO] |  |  |  +- com.fasterxml.jackson.core:jackson-annotations:jar:2.9.0:compile
[INFO] |  |  |  \- com.fasterxml.jackson.core:jackson-core:jar:2.9.5:compile
[INFO] |  |  +- com.fasterxml.jackson.datatype:jackson-datatype-jdk8:jar:2.9.5:compile
[INFO] |  |  +- com.fasterxml.jackson.datatype:jackson-datatype-jsr310:jar:2.9.5:compile
[INFO] |  |  \- com.fasterxml.jackson.module:jackson-module-parameter-names:jar:2.9.5:compile
[INFO] |  +- org.springframework.boot:spring-boot-starter-tomcat:jar:2.0.1.RELEASE:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-core:jar:8.5.29:compile
[INFO] |  |  +- org.apache.tomcat.embed:tomcat-embed-el:jar:8.5.29:compile
[INFO] |  |  \- org.apache.tomcat.embed:tomcat-embed-websocket:jar:8.5.29:compile
[INFO] |  +- org.hibernate.validator:hibernate-validator:jar:6.0.9.Final:compile
[INFO] |  |  +- javax.validation:validation-api:jar:2.0.1.Final:compile
[INFO] |  |  +- org.jboss.logging:jboss-logging:jar:3.3.2.Final:compile
[INFO] |  |  \- com.fasterxml:classmate:jar:1.3.4:compile
[INFO] |  +- org.springframework:spring-web:jar:5.0.5.RELEASE:compile
[INFO] |  \- org.springframework:spring-webmvc:jar:5.0.5.RELEASE:compile
[INFO] |     +- org.springframework:spring-aop:jar:5.0.5.RELEASE:compile
[INFO] |     +- org.springframework:spring-context:jar:5.0.5.RELEASE:compile
[INFO] |     \- org.springframework:spring-expression:jar:5.0.5.RELEASE:compile

```

