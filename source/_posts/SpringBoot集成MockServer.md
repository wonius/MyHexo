---
title: SpringBoot集成MockServer
copyright: true
date: 2018-04-25 23:25:25
tags: 
- SpringBoot
- MockServer
categories: 
- Spring
- SpringBoot
keywords: 
- SpringBoot
- MockServer
description: SpringBoot集成MockServer，将http接口进行mock
---

## 背景

最近项目中在引入Mock，其中有一项是屏蔽掉外部依赖，包括关联系统的REST接口。做了下技术调研，打算使用MockServer对外部接口进行mock

## 插件集成

1. 在pom文件中增加mockserver的maven插件：

   ```xml
   <plugin>
       <groupId>org.mock-server</groupId>
       <artifactId>mockserver-maven-plugin</artifactId>
       <version>5.3.0</version>
       <configuration>
           <serverPort>1080</serverPort>
           <!-- 可根据情况是否设置porxyPort。我没设置 -->
           <proxyPort>1090</proxyPort>
           <logLevel>DEBUG</logLevel>
   <!-- 该类为初始化类，按照自己的文件配置，即下一步中的类 -->        <initializationClass>org.mockserver.maven.ExampleInitializationClass</initializationClass>
       </configuration>
       <executions>
           <execution>
           	<!-- 指定在该生命周期时调用该插件 -->
               <id>process-test-classes</id>
               <phase>process-test-classes</phase>
               <goals>
                   <goal>start</goal>
               </goals>
           </execution>
           <execution>
               <id>verify</id>
               <phase>verify</phase>
               <goals>
                   <goal>stop</goal>
               </goals>
           </execution>
       </executions>
   </plugin>
   ```

2. 创建自己的MockServerInitializer类（**类名一定不要定义为`MockServerInitializer`，官方有这个类**），实现ExpectationInitializer接口：

   ```
   public class HttpMockServerInitializer implements ExpectationInitializer {

       @Override
       public void initializeExpectations(MockServerClient mockServerClient) {
           mockServerClient.when(HttpRequest.request().withMethod("POST").withPath("/test"))
                   .respond(HttpResponse.response().withStatusCode(200).withBody("success"));
       }
   }
   ```

   *PS：如果你在用idea编辑器，一定要打开idea的自动编译功能，默认该功能是不开启的。打开方式*

3. 运行mockserver服务：

   ```
   mvn mockserver:run
   ```

   然后就可以使用postman请求，得到预设的返回。

## 代码手动创建

插件集成比较方便，不过有些场景无法满足。比如需要在MockServer中注入Spring容器中的对象，当运行MockServer时，由于没有初始化Spring容器，导致注入失败。

这种情况就需要手动创建MockServer。

1. 编辑pom.xml，导入依赖：

   ```
   <dependency>
       <groupId>org.mock-server</groupId>
       <artifactId>mockserver-netty</artifactId>
       <!-- 成文时最新版本，可以到官网查询目前最新版本 -->
       <version>5.3.0</version>
   </dependency>
   ```

2. 创建mockServer，proxy可不设：

   ```
   //引入类
   import static org.mockserver.integration.ClientAndProxy.startClientAndProxy;
   import static org.mockserver.integration.ClientAndServer.startClientAndServer;

   /**
    * 使用@Service将该类注入spring，方便管理
    * 实现InitializingBean接口，初始化类对象时，实例化mockServer对象
    * 实现DisposableBean接口，销毁类对象时，关闭服务
    */
   @Service
   public class HttpMockServerManual implements InitializingBean, DisposableBean {

       //创建对象
       private ClientAndProxy proxy;
       private ClientAndServer mockServer;

       @Override
       public void destroy() throws Exception {
           //别忘了关闭服务
           proxy.stop();
           mockServer.stop();
       }

       @Override
       public void afterPropertiesSet() throws Exception {
           //实例化对象
           mockServer = startClientAndServer(1080);
           proxy = startClientAndProxy(1090);
       }

   }
   ```

3. mock相关http接口：

   ```
   mockServer.when(HttpRequest.request().withMethod("POST").withPath("/test"))
                   .respond(HttpResponse.response().withStatusCode(200).withBody("success"));
   ```

   

## 更多详情参见官网

本文只提供了java实现。官网还有JS等实现，请戳[MockServer官网](http://mock-server.com/#what-is-mockserver)了解。