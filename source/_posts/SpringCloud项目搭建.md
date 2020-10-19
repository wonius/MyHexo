---
title: SpringCloud项目搭建
copyright: true
date: 2018-05-31 17:58:25
tags: SpringCloud
categories:
- Spring
- SpringCloud
keywords: 
- SpringCloud
- Eureka
description: SpringCloud聚合项目搭建，创建Eureka注册中心和服务。
---

为了便于管理，将所有项目放在一个工程中维护。

# 创建parent工程

首先，new一个Project，选择maven，注意不要勾选`create from archetype`，填写GroupId、ArtifactId、Project name。创建完成后，将src目录删除。

![parent工程](../SpringCloud项目搭建/parent.png)

配置pom文件，添加SpringCloud依赖

```
<packaging>pom</packaging>

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.2.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
    <spring-cloud.version>Finchley.RC2</spring-cloud.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<dependencies>
    <!--各个子项目共用的一些依赖-->
    <!--test-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    
    <!--springboot-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

<repositories>
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/milestone</url>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

# 创建Eureka注册中心

创建一个新的Module，选择Spring Initializr，填写GroupId、ArtifactId、Name，选择Cloud Discovery - Eureka Server，命名Module Name，完成。

![创建Eureka](../SpringCloud项目搭建/eureka.png)

创建后会自动在`parent`的pom中添加模块，如果没有，手动添加，如下：

```
<modules>
    <module>eureka-server</module>
    <module>service</module>
</modules>
```

编辑该模块中的pom文件：

```
<parent>
    <groupId>com.woniu</groupId>
    <artifactId>springcloud-parent</artifactId>
    <version>1.0-SNAPSHOT</version>
</parent>

<modelVersion>4.0.0</modelVersion>
<artifactId>eureka-server</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>jar</packaging>

<name>eureka-server</name>
<description>Eureka-Server</description>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>
```

对应Application类添加注解：

```
@SpringBootApplication
@EnableEurekaServer
public class EurekaServersApplication {

	public static void main(String[] args) {
		SpringApplication.run(EurekaServersApplication.class, args);
	}
}
```

编辑application.yml配置文件：

```
server:
  port: 8081
eureka:
  instance:
    hostname: localhost
# 该server默认同时也是client
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

# 创建服务

创建一个新的Module，仍然选择Maven，勾选`create from archetype`，并选择quickstart，填写ArtifactId、Module Name，创建完成。

![创建service](../SpringCloud项目搭建/service.png)

修改pom文件：

```
<parent>
    <artifactId>springcloud-parent</artifactId>
    <groupId>com.woniu</groupId>
    <version>1.0-SNAPSHOT</version>
</parent>
<modelVersion>4.0.0</modelVersion>
<artifactId>service</artifactId>
<name>service</name>
<packaging>jar</packaging>

<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

在main下面创建resources目录，该目录下创建application.yml文件：

```
spring:
  application:
    name: service
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8081/eureka/
server:
  port: 8082
```

在对应Application类添加注解：

```
@SpringBootApplication
@EnableEurekaClient
@RestController
public class ServiceApplication 
{
    @GetMapping("/hello")
    public String service(){
        return "world";
    }
    
    public static void main( String[] args )
    {
        SpringApplication.run(ServiceApplication.class, args);
    }
}
```

最后依次启动eureka-server、service服务，访问localhost:8081，访问成功。

![run](../SpringCloud项目搭建/run.png)

*@EnableDiscoveryClient、@EnableEurekaClient区别：*

*@EnableDiscoveryClient基于spring-cloud-commons，其discovery service有多种实现，Eureka、Consul、Zookeeper等。而@EnableEurekaClient只针对spring-cloud-netflix服务；*

*@EnableEurekaClient实现中包含@EnableDiscoveryClient；*