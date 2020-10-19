---
title: Docker常用方法
date: 2018-03-18 13:24:40
tags: Docker
categories: Docker
keywords: Docker
description: Docker安装、设置，以及Dockerfile常用语法
copyright: true
---

# 安装Docker

```
//源中有，直接yum安装
yum install docker-io -y

//查看docker版本
docker -v

//启动
service docker start

//设置开机自启
chkconfig docker on
```

# 配置Docker

```
//设置腾讯云镜像源
echo "OPTIONS='--registry-mirror=https://mirror.ccs.tencentyun.com'" >> /etc/sysconfig/docker

//重新加载服务，重启
systemctl daemon-reload
service docker restart
```

# 安装镜像

```
//拉取一个centos镜像
docker pull centos

//查看docker中有的镜像
docker images
```

# 运行镜像

```
//运行docker容器中的centos，并使用bash shell
docker run -it centos /bin/bash

//直接运行镜像
docker run -it -p port:port centos

//登录后可以按自己需要做操作
······

//退出镜像
exit
```

# 保存镜像

```
//查看容器信息，获取容器id
docker ps -a

//保存
docker commit -m="你的备注" 容器id 镜像id
```

# 打包镜像

```
docker build -t imageName .

// 查看镜像
docker images | grep imageName
```

# 删除镜像

```shell
docker images 
docker rmi imageid

// 如果已经运行，先删除容器id
docker ps -a
docker rm containerid
docker rmi imageid

// 如果有镜像from该镜像，先删除子镜像(报image has dependent child images)
// 查看有哪些依赖子镜像
docker image inspect --format='{{.RepoTags}} {{.Id}} {{.Parent}}' $(docker image ls -q --filter since=*****)
// 删除子镜像
docker rmi *** *** ***
```

> Tips:
>
> 如果镜像被覆盖，导致后面的tag为`<none>`，无法对其进行操作的话：
>
> docker tag imgId xxxxx
>
> docker rmi xxxxx

# 导入、导出镜像

```shell
# export & import（容器操作）
docker export -o xxx.tar xxxid(容器id)
cat xxx.tar | docker import - xxxname(镜像名xxx/xxx:1.0)

# save & load（镜像操作）
docker save -o xxx.tar xxxid(容器id)
docker load -I xxx.tar
```



# 镜像仓库

```shell
# 登录远程仓库
docker login hub.yun.***** -u xxx

# 推送镜像到远程仓库，需先登录
docker push imageName

# 查看远程仓库中的镜像
docker search imageName
```



# Dockerfile语法

```
//拉取基础镜像
FROM <IMAGE>

//设置镜像作者
MAINTAINER <NAME> <EMAIL>

//为后续的 RUN 、 CMD 、 ENTRYPOINT 指定工作目录
WORKDIR <PATH>

//创建镜像时执行
RUN <COMMAND>

//镜像创建成功后，在镜像中执行。如果有多条，只执行最后一条。可被docker run覆盖
CMD ["executable", "param1", "param2"]使用exec执行，推荐 
CMD command param1 param2，在/bin/sh上执行 
CMD ["param1", "param2"] 提供给ENTRYPOINT做默认参数。

//与CMD类似。不可被docker run覆盖，除非加上--entrypoint
ENTRYPOINT command param1 param2

//复制，src可以为Dockerfile所在目录的相对路径，也可以是url。如果是url，会下载并复制到dest；可自动解压
ADD <SRC> <DEST>

//复制，src为Dockerfile所在目录的相对路径
COPY <SRC> <DEST>

//容器暴露的端口号。多个以空格间隔
EXPOSE <PORT>

//设置环境变量
ENV <KEY> <VALUE>

//指定运行容器时的用户名或UID，后续的RUN、CMD、ENTRYPOINT使用该用户
USER daemon

//在主机上创建一个挂载，挂载到容器的指定路径。不如docker run -v强大
VOLUME <PATH>

//其他镜像FROM该镜像时，执行
ONBUILD [INSTRUCTION]
```

举个🌰

```
FROM centos
MAINTAINER nobody "xx@163.com"
RUN mkdir -p /opt/jdk/
RUN mkdir -p /opt/tomcat/
ADD jdk1.7.0_79 /opt/jdk/
ADD tomcat  /opt/tomcat/
ENV CATALINA_HOME /opt/tomcat
ENV JAVA_HOME /opt/jdk
EXPOSE 8080
ENV PATH $PATH:$JAVA_HOME/bin
CMD ["/opt/tomcat/bin/catalina.sh","run"]
```

