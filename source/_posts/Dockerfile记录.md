---
title: Dockerfile记录
copyright: true
date: 2018-09-21 12:04:05
tags: Docker
categories: Docker
keywords: Dockerfile
description: Dockerfile记录
---

# dockerfile1

```dockerfile
FROM hub.yun.paic.com.cn/library/tomcat:8.5-jre8

ENV USER_NAME woniu
ENV USER_UID 789
ENV ADMIN_NAME admin
ENV USER_GROUP_ID 5682
ENV ADMIN_GROUP_ID 603

RUN groupadd -g $USER_GROUP_ID $USER_NAME && groupadd -g $ADMIN_GROUP_ID -u $ADMIN_NAME \
&& useradd -l -r $USER_NAME -g $USER_NAME -u $USER_UID \
&& usermod -a -G $ADMIN_NAME  $USER_NAME \
&& mkdir /usr/local/tomcat/hello && chown -R $USER_NAME:$USER_NAME /usr/local/tomcat

COPY catalina.sh /usr/local/tomcat/bin/catalina.sh
RUN chown $USER_NAME:$USER_NAME /usr/local/tomcat/bin/catalina.sh
RUN chmod 750 /usr/local/tomcat/bin/catalina.sh

USER $USER_NAME
```

# dockerfile2

该dockerfile基于上一个，由于上一个dockerfile最后切换到用户woniu，没有mkdir权限，所以需要先切换到root用户。

```dockerfile
FROM hub.yun.paic.com.cn/library/tomcat:1.0

USER root
RUN mkdir -p /wls/wls81/agent
COPY agent /wls/wls81/agent
RUN chown -R $USER_NAME:$USER_NAME /wls/wls81

COPY catalina.sh /usr/local/tomcat/bin/catalina.sh
RUN chown $USER_NAME:$USER_NAME /usr/local/tomcat/bin/catalina.sh
RUN chmod 750 /usr/local/tomcat/bin/catalina.sh

USER $USER_NAME
```

