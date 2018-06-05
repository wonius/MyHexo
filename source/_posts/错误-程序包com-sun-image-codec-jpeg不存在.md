---
title: com.sun.image.codec.jpeg不存在
date: 2017-09-18 17:51:00
tags: Java
categories: JAVA
keywords: 
- Java
- JPEGCodec
description: com.sun.image.codec.jpeg不存在
copyright: true
---

## 问题

​	最近项目中有个应用，需要将上传图片压缩为缩略图，用到了JPEGCodec等类。开发过程中一切正常，程序也能运行。当使用maven发布时，程序报错了：

{% asset_img jpeg_error.png 错误信息%}

## 过程

​	度娘了一下，maven的插件maven-compiler-plugin在编译的时候，没有加载/jre/lib内的包。而我们用到的JPEGCodec等类，恰好在/jre/lib/rt.jar包内。为了解决该问题，我们可以使用bootclasspath标签。该标签会将你指定路径下的包，通知到maven进行加载。

```
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-compiler-plugin</artifactId>
	<version>2.0.2</version>
	<configuration>
		<source>1.7</source>
		<target>1.7</target>
		<encoding>UTF8</encoding>
		<!-- 设置指定包的路径 -->
		<compilerArguments>
		<verbose />
		<!-- 有两点需要注意 -->
		<bootclasspath>${env.JAVA_HOME}/jre/lib/rt.jar:${env.JAVA_HOME}/jre/lib/jce.jarr</bootclasspath>
		</compilerArguments>
	</configuration>
</plugin>
```

Attention:

1. ${JAVA_HOME}设置为全局变量。我用的是Mac+IDEA，刚开始改过之后，IDEA的编辑器显示一直为红色。因为过度相信工具，所以一直以为全局变量设置的有问题，走了弯路。所以只要echo正常，就不要怀疑。

   {% asset_img JAVA_HOME_ERROR.png IDEA下JAVA_HOME显示为红色%}

2. 当bootclasspath标签内，有多个变量时，Mac/Linux系统以 ：分隔，Windows系统以 ; 分隔。否则也会有异常。​


