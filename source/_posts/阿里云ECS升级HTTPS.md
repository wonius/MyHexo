---
title: 阿里云ECS升级HTTPS
date: 2018-02-06 00:29:32
tags:
- SSL
categories:
- 安全
keywords:
- 安全
- SSL
- https
description: 阿里云主机，通过阿里云盾制作证书，开启SSL，使用https协议
copyright: true
---

## 背景

​	最近系统准备开发小程序，增加流量入口，而小程序的后端接口需要支持https协议，顺便给网站安全升下级。

## 制作证书

​	阿里云上有证书服务，填写完整信息可以在线制作，省时省力。登录阿里云后，点开安全（云盾）- CA证书服务，点击右上角购买证书，填写信息，域名验证类型选择DNS，因为域名也是在阿里买的，所以勾上自动完成域名授权验证，完成。很快，10min左右就审核通过了。

## 配置安全组

​	在ECS中点击管理实例，添加安全组规则，公网入方向中加入443/443，地址段为0.0.0.0/0。

## 防火墙打开443端口

​    打开防火墙端口

```shell
# 编辑
vim /etc/sysconfig/iptables
# 添加记录
-A INPUT -p tcp -m tcp --dport 443 -j ACCEPT 
# 重启服务
service iptables restart
```

## Nginx配置

​	将证书下载，在服务器上nginx目录下建立/cert，将证书解压，scp到/cert目录下：

```shell
#在conf.d下建立单独conf文件
vim ssl.yuming.conf

#增加如下配置
server {
  listen       443;
  server_name  域名;
  ssl          on;
  root html;
  index index.html; 

  ssl_certificate   /etc/nginx/cert/XXXXXXXXXX.pem;
  ssl_certificate_key  /etc/nginx/cert/XXXXXXXXXXX.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers AESGCM:ALL:!DH:!EXPORT:!RC4:+HIGH:!MEDIUM:!LOW:!aNULL:!eNULL;
  ssl_prefer_server_ciphers on;
  
  #需要加上，一开始没加，访问一直404，找了半天原因
  location / {
		proxy_pass	http://localhost:8080;
		proxy_set_header Host	$host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_set_header X-Forwarded-Proto  $scheme; 		#与下方tomcat配置对应，解决request.getScheme()问题。不需要可以不用配
	}
}

# 重启nginx
service nginx restart
```

## Tomcat配置

​	做完上面的配置之后，可以通过https协议访问网站，不过代码中使用request.getScheme()取到的协议仍是http，不是https，有报错，还需在server.xml中的Engine模块中添加，需重启。

```shell
<Valve className="org.apache.catalina.valves.RemoteIpValve"  
remoteIpHeader="X-Forwarded-For"  
protocolHeader="X-Forwarded-Proto"  
protocolHeaderHttpsValue="https"/>
```

## one more thing

​	如果需要将所有请求强制使用https协议，在监听80端口处增加如下配置：

```shell
rewrite ^(.*)$ https://${server_name}$1 permanent;
```
