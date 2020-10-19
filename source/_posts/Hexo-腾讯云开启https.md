---
title: 腾讯云+Hexo支持https协议
copyright: true
date: 2018-08-23 09:20:58
tags: Hexo
categories: Hexo
keywords: 
- Hexo
- https
description: 腾讯云+Hexo构建的博客系统，开启https证书认证
---

# 申请证书

1. 登录腾讯云管理平台；
2. 打开云产品—域名与网站—SSL证书管理，点击申请证书（也可以自己制作证书。不过使用腾讯云的服务更简单一点，阿里云也有制作证书的功能）；
3. 填好信息，下一步；
4. 域名身份验证，支持两种方式，我用的是手动DNS验证，在域名解析添加一条解析记录就可以了；
5. 等待审核，一般一个工作日就审核通过；
6. 审核通过后，下载证书；

*.crt：自签名的证书*
*.csr：证书的请求(用于向证书颁发机构申请crt证书时使用，nginx配置时不会用到)*
*.key：SSL Key (分为不带口令和带口令版本)。*
*签名证书配置nginx需要的是.crt证书，和不带口令的SSL Key的.key文件。*

# 配置nginx

1. 将下载好的证书上传到服务器，指定目录保存；

2. 编辑nginx解析规则，添加443端口解析（编辑前记得备份，养成良好习惯）：

   ```
   server {
           listen 443;
           server_name _; #填写绑定证书的域名
           ssl on;
           
           ssl_certificate /****/1_wonius.top_bundle.crt; #证书路径
           ssl_certificate_key /****/2_wonius.top.key;
           ssl_session_timeout 5m;
           ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #按照这个协议配置
           ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;#按照这个套件配置
           ssl_prefer_server_ciphers on;
           location / {
               root   /data/www/hexo; #站点目录
               index  index.html index.htm;
           }
       }
   ```

3. （可选）全站加密，http自动跳转https，修改80端口规则：

   ```
   server {
           listen       80;
           server_name  _;
           rewrite ^(.*) https://$host$1 permanent;
       }
   ```

4. 重启nginx`service nginx restart`；

5. 然后访问，是不是就有小绿锁🔐了

# one more thing

开启https之后，如果内部有一些依赖服务不支持https，那这些服务将不可用。