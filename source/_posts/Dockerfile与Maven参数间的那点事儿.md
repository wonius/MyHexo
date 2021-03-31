---
title: Dockerfile与Maven参数间的那点事儿
copyright: true
date: 2018-09-28 11:07:34
tags: Docker
categories: Docker
keywords: Dockerfile
description: Maven设置Dockerfile参数
---

# Maven设置Dockerfile参数

项目中需要根据不同环境，镜像里tomct中catalina.sh里某个参数值也要不同。

项目中用了docker-maven-plugin插件管理docker，所以想到通过maven参数进行设置。

## pom配置

```
# 在docker-maven-plugin插件中增加配置
<plugin>
	...
	<configuration>
		...
		<buildArgs>
			# 标签名为key，标签值为value
			<APP_NAME>${appName}</APP_NAME>
		</buildArgs>
		...
	</configuration>
	...
</plugin>

# 环境配置
<profiles>
	<profile>
		<id>ci</id>
		<properties>
			<appName>hello-ci</appName>
		</properties>
	</profile>
	...
</profiles>
```

## dockerfile配置

```dockerfile
...
# 定义参数
ARG APP_NAME
# 增加CATALINA的环境变量
ENV CATALINA_OPTS -DapplicationName=$APP_NAME
```

## one more thing

除了用docker插件，还可以是用exec插件，运行shell脚本或java文件，这种方式更自由一点。