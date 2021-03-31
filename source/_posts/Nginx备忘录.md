---
title: Nginx备忘录
copyright: true
date: 2019-11-12 09:30:03
tags: nginx
categories: 中间件
keywords: nginx
description: nginx的一些信息
---

# 安装

```bash
# centos
yum install nginx
# ubuntu
sudo apt-get install nginx;
# mac
brew install nginx
# 或者编译源码包，不推荐。
# 先下载，解压
./configure			# 可以通过-prefix=PATH 指定安装目录
make & make install
```

# 相关命令

```bash
# mac中nginx默认安装在/usr/local/Cellar/nginx，同时也会加入到/usr/local/bin，进入
# 启动。默认加载conf中的nginx.conf配置文件
./nginx 或 ./nginx -s start
# 热启动(不down机更新配置)
./nginx -s reload
# 关闭
./nginx -s stop
# 修改*.conf配置文件后，检查语法是否正常
nginx -t

# http默认端口80
# https默认端口443
```

# 目录结构

```bash
# nginx目录下
confg/	# 配置文件目录
logs/	# 日志目录
html/	# 网站文件目录
sbin/	# 命令目录
conf.d/	# 有些安装完成之后会有这个目录，也是配置目录。nginx启动时会优先去加载config/nginx.conf，如果没有，加载该目录中的配置。
```

# nginx.conf配置

使用mac的brew安装，目录在/usr/local/etc/nginx

```nginx
main
events {
  ....
}
http {
  ....
  upstream myproject {
    .....
  }
  server {
    ....
    location {
        ....
    }
  }
  server {
    ....
    location {
        ....
    }
  }
  ....
}
```

nginx配置文件主要分为六个区域：
`main(全局设置)`、`events(nginx工作模式)`、`http(http设置)`、
`sever(主机设置)`、`location(URL匹配)`、`upstream(负载均衡服务器设置)`。

## main模块

main是全局配置：

```nginx
user nobody nobody;
worker_processes auto;

# 根据情况自己制定日志目录
error_log  /usr/local/var/nginx/error.log  notice;
pid        /usr/local/var/nginx/nginx.pid;

worker_rlimit_nofile 100000;
```

`user` 来指定Nginx Worker进程运行用户以及用户组，默认由nobody账号运行。

`worker_processes`来指定了Nginx要开启的子进程数。每个Nginx进程平均耗费10M~12M内存。最优值取决于许多因素，包括（但不限于）CPU核的数量、存储数据的硬盘数量及负载模式。不能确定的时候，将其设置为可用的CPU内核数将是一个好的开始（设置为“auto”将尝试自动检测它）。该值设置的是子进程worker数量，同时还有一个主进程master，比如设置值为2时，总共有3个进程。

`error_log`用来定义全局错误日志文件。日志输出级别有debug、info、notice、warn、error、crit可供选择，其中，debug输出日志最为最详细，而crit输出日志最少。

`pid`用来指定进程id的存储文件位置。

`worker_rlimit_nofile`用于指定一个nginx进程可以打开的最多文件描述符数目，受限于操作系统的配置。如果你的nginx报错“too many open files”，就是操作系统的值比你设置的值小。可以通过`ulimit -n`查看操作系统的值。通过`ulimit -n 65535`修改，该命令将操作系统修改为65535。

## events模块

events模块来用指定nginx的工作模式和工作模式及连接数上限：

```nginx
events {
    use kqueue; #mac平台
    worker_connections  1024;
}
```

`use`用来指定Nginx的工作模式。Nginx支持的工作模式有select、poll、kqueue、epoll、rtsig和/dev/poll。其中select和poll都是标准的工作模式，kqueue和epoll是高效的工作模式，不同的是epoll用在Linux平台上，而kqueue用在BSD系统中，因为Mac基于BSD,所以Mac也得用这个模式，对于Linux系统，epoll工作模式是首选。

`worker_connections`用于定义Nginx每个进程的最大连接数，即接收前端的最大请求数，默认是1024。最大客户端连接数由`worker_processes`和`worker_connections`决定,即`Max_clients=worker_processes*worker_connections`。在作为反向代理时，Max_clients变为：`Max_clients = worker_processes * worker_connections/4`。（关于max_clients值的计算，官方文档也没有给出明确公式，网上说法也不一。可以看下这个，我觉得还挺靠谱：[Max_clients计算公式](https://www.cnblogs.com/tangchuanyang/p/6076725.html)）
进程的最大连接数受Linux系统进程的最大打开文件数限制（每个TCP连接都要创建一个socket句柄，每个socket句柄同时也是一个文件句柄），只有操作系统中的`ulimit -n`的值比worker_connections的值大，该设置才有效。

## http模块

http模块是最核心的模块，它负责HTTP服务器相关属性的配置，主要包含server和upstream子模块。

```nginx
http{
    include       mime.types;
    default_type  application/octet-stream;
    log_format  myFormat  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log  /usr/local/var/nginx/access.log  myFormat;
    
    server_tokens on;
    
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    
	······
}
```

`include` 可以用来加载其他配置文件，如`include /etc/nginx/conf.d/*.conf`。上例中用来引入设定文件的mime类型，类型在配置文件目录下的mime.type文件定义，来告诉nginx来识别文件类型。

`default_type`默认文件类型，当文件类型未定义时使用该值，值默认为`text/plain`。通常修改为`application/octet-stream`，使用二进制流的方式解析。

`log_format`设置日志的格式，`myFormat`相当于别名，配合`access_log`使用。

`access_log` 访问日志存放目录。nginx调优时可以关闭该功能，提高磁盘IO读写速度。

`server_tokens` 对于性能没有优化，当访问失败时，可以隐藏nginx版本信息。

`sendfile` 可以使用sendfile()，使文件传输更高效。传输文件涉及到将内容从磁盘读取，写到Tcp连接的过程。该项关闭时的流程是，Pre-sendfile是传送数据之前在用户空间申请数据缓冲区。之后用read()将数据从文件拷贝到内核读取缓冲区，再从内核读取缓冲区读到用户缓冲区，write()将用户缓冲区数据写到内核Socket缓冲区，再写入网络协议引擎，进行传输。sendfile()可以在任意两个文件描述符之间互相拷贝数据，而sendfile()是可以立即将数据从磁盘读，经OS缓存，写入TCP socket，不经过用户缓冲区。因为这种拷贝是在内核完成的（避免了内核缓冲区和用户缓冲区之间的数据拷贝），sendfile()要比组合read()和write()以及打开关闭丢弃缓冲更加有效。

`tcp_nopush` 告诉nginx在一个数据包里发送所有头文件，而不一个接一个的发送。

`tcp_nodelay` 告诉nginx不要缓存数据，而是一段一段的发送。当需要及时发送数据时，就应该给应用设置这个属性，这样发送一小块数据信息时就不能立即得到返回值。`tcp_nopush`和`tcp_nodelay`两个指令设置为on用于防止网络阻塞。

```nginx
http{
	······
	
  keepalive_timeout  10;
    
  client_header_timeout 10;
	client_body_timeout 10;

	reset_timedout_connection on;
	send_timeout 10;
	
	limit_conn_zone $binary_remote_addr zone=addr:5m;
	limit_conn addr 100;
	
	······
}
```

`keepalive_timeout`设置客户端连接保持活动的超时时间。在超过这个时间之后，服务器会关闭该连接。

`client_header_timeout` 请求头超时时间。

`client_body_timeout` 请求体超时时间。

`reset_timeout_connection` 关闭不响应的客户端连接。这将会释放那个客户端所占有的内存空间。

`send_timeout`  指定客户端的响应超时时间。这个设置不会用于整个转发器，而是在两次客户端读取操作之间。如果在这段时间内，客户端没有读取任何数据，nginx就会关闭连接。

`limit_conn_zone` 设置addr连接的共享内存为5m。$binary_remote_addr的长度为 4 bytes，会话信息的长度为 32 bytes，5m大约可以记录`5*1k*32 `个会话信息（一个会话占用 32 bytes。如果一个会话占用64bytes，可保存`5*1k*16`个会话）。

`limit_conn` 设置addr的最大连接数为100，每个ip最多同时开100个连接。

```nginx
http{
	······
	
	# gzip配置，可根据情况关闭
    gzip on;
    gzip_disable "msie6";

    # gzip_static on;
    gzip_proxied any;
    gzip_min_length 1000;
    gzip_comp_level 4;

    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
	
	······
}
```

`gzip` 采用gzip压缩的形式发送数据。可以减少发送的数据量。

`gzip_disable`为指定的客户端禁用gzip功能。我们设置成IE6或者更低版本以使我们的方案能够广泛兼容。

`gzip_static`告诉nginx在压缩资源之前，先查找是否有预先gzip处理过的资源。这要求你预先压缩你的文件（在这个例子中被注释掉了），从而允许你使用最高压缩比，这样nginx就不用再压缩这些文件了（想要更详尽的gzip_static的信息，请点击这里）。

`gzip_proxied`允许或者禁止压缩基于请求和响应的响应流。我们设置为any，意味着将会压缩所有的请求。

`gzip_min_length`设置对数据启用压缩的最少字节数。如果一个请求小于1000字节，我们最好不要压缩它，因为压缩这些小的数据会降低处理此请求的所有进程的速度。

`gzip_comp_level`设置数据的压缩等级。这个等级可以是1-9之间的任意数值，9是最慢但是压缩比最大的。我们设置为4，这是一个比较折中的设置。

`gzip_types`设置需要压缩的数据格式。

```nginx
http{
	······
	
	# 文件配置，可根据情况配置，现在好多都不配置了，仅当了解吧
    open_file_cache max=100000 inactive=20s; 
    open_file_cache_valid 30s; 
    open_file_cache_min_uses 2;
    open_file_cache_errors on;
	
	······
}
```

`open_file_cache`打开缓存的同时也指定了缓存最大数目，以及缓存的时间。我们可以设置一个相对高的最大时间，这样我们可以在它们不活动超过20秒后清除掉。

`open_file_cache_valid` 在open_file_cache中指定检测正确信息的间隔时间。

`open_file_cache_min_uses` 定义了open_file_cache中指令参数不活动时间期间里最小的文件数。

`open_file_cache_errors`指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件。我们也包括了服务器模块，这些是在不同文件中定义的。如果你的服务器模块不在这些位置，你就得修改这一行来指定正确的位置。

## server模块

server模块是http的子模块，用来定义虚拟主机的配置。一个http中可以有多个server。

```nginx
server {
        listen       8080;
        server_name  localhost 127.0.0.1 www.xxx.com;
        # 全局定义，如果都是这一个目录，这样定义最简单。
        root   /Users/xxx/www;
        index  index.php index.html index.htm; 
        charset utf-8;
        access_log  usr/local/var/host.access.log  main;
        aerror_log  usr/local/var/host.error.log  error;
        ....
}
```

`listen`用于指定虚拟主机的服务端口。
`server_name`用来指定IP地址或者域名，多个域名之间用空格分开。
`root` 表示在这整个`server`虚拟主机内，全部的root web根目录。注意要和`locate {}`下面定义的区分开来。
`index` 全局定义访问的默认首页地址。注意要和`locate {}`下面定义的区分开来。
`charset`用于设置网页的默认编码格式。
`access_log`用来指定此虚拟主机的访问日志存放路径，最后的main用于指定访问日志的输出格式。

## location模块

nginx中最核心的模块，用来配置请求的路由，以及各种页面的处理情况。支持正则表达式、条件判断，同时可以设置负载均衡、反向代理、虚拟域名等。

### 匹配规则

location的url匹配具有一定优先级，其优先级如下：

```nginx
location [ = | ~ | ~* | ^~ ]  uri  { }
# =开头表示精确匹配
# ^~ 开头表示uri以某个常规字符串开头，这个不是正则表达式
# ~ 开头表示区分大小写的正则匹配;
# ~* 开头表示不区分大小写的正则匹配
# / 通用匹配, 如果没有其它匹配,任何请求都会匹配到

# 优先级
(location =) > (location 完整路径) > (location ^~ 路径) > (location ~* 正则顺序) > (location 部分起始路径) > (/)
```

### 语法

语法方面，主要有：

```nginx
location /static {
    root   /var/app/www;
    # alias  /var/app/www; 
    index  index.php index.html index.htm;
    proxy_pass http://127.0.0.1:8080/;
}
```

`root` 用于指定当访问`/static`目录时，所对应的主机对应的目录，为`/var/app/www/static`。

`alias` 与root类似，但是区别在于当访问`/static`时，对应的路径为`/var/app/www`。

`index` 默认首页，按先后顺序依次查找。

`try files` 至少两个参数，按照顺序进行访问尝试，最后一个参数最好配置为静态页面，防止死循环。

`rewite` 将一个url重写成另一个url。

`proxy_pass` 反向代理配置，代理请求。其具体用法：

```nginx
# 当访问http://127.0.0.1/proxy/1.png时

# 代理到http://127.0.0.2/1.png
location /proxy/ {
    proxy_pass http://127.0.0.2/;
}

# 代理到http://127.0.0.2/proxy/1.png
location /proxy/ {
    proxy_pass http://127.0.0.2;
}

# 代理到http://127.0.0.2/img/1.png
location /proxy/ {
    proxy_pass http://127.0.0.2/img/;
}

# 代理到http://127.0.0.2/img1.png
location /proxy/ {
    proxy_pass http://127.0.0.2/img;
}
```

## upstream模块

upstream模块负责负载均衡。

```nginx
upstream baidu.com{
    ip_hash;	# 负载算法
    server xx:xx:xx:1:8080;		# server列表
    server xx:xx:xx:1:8080 down;
    server xx:xx:xx:1:8080  max_fails=3  fail_timeout=20s;
    server xx:xx:xx:1:8080;
}
```

**5种负载算法：**

`轮询(默认)` 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端某台服务器宕机，则自动剔除故障机器，使用户访问不受影响。

`weight` 指定轮询权重，weight值越大，分配到的几率就越高，主要用于后端每台服务器性能不均衡的情况。

`ip_hash` 每个请求按访问IP的哈希结果分配，这样每个访客固定访问一个后端服务器，可以有效的解决动态网页存在的session共享问题。

`fair(第三方)` 更智能的一个负载均衡算法，此算法可以根据页面大小和加载时间长短智能地进行负载均衡，也就是根据后端服务器的响应时间来分配请求，响应时间短的优先分配。如果想要使用此调度算法，需要Nginx的upstream_fair模块。

`url_hash(第三方)` 按访问URL的哈希结果来分配请求，使每个URL定向到同一台后端服务器，可以进一步提高后端缓存服务器的效率。如果想要使用此调度算法，需要Nginx的hash软件包。

**另外，每台server还可以设置不同的负载状态：**

`down` 表示当前server暂时不参与负载均衡。

`backup` 预留的备份机，当其他所有非backup机器出现故障或者繁忙的时候，才会请求backup机器，这台机器的访问压力最轻。

`max_fails` 允许请求的失败次数，默认为1，配合fail_timeout一起使用。

`fail_timeout` 经历max_fails次失败后，暂停服务的时间，默认为10s（某个server连接失败了max_fails次，则nginx会认为该server不工作了。同时，在接下来的 fail_timeout时间内，nginx不再将请求分发给失效的server）。

当负载调度算法为**ip_hash**时，后端服务器在负载均衡调度中的状态不能是weight和backup。

# 例子 

```nginx
########### 每个指令必须有分号结束。#################

#配置用户或者组，默认为nobody nobody。
#user administrator administrators; 
#允许生成的进程数，默认为1
#worker_processes 2; 
#指定nginx进程运行文件存放地址
#pid /nginx/pid/nginx.pid; 
#制定错误日志路径，级别。这个设置可以放入全局块，http块，server块，级别依次为：debug|info|notice|warn|error|crit|alert|emerg
error_log log/error.log debug; 

#工作模式及连接数上限
events {
    #设置网路连接序列化，防止惊群现象发生，默认为on
    accept_mutex on; 
    #设置一个进程是否同时接受多个网络连接，默认为off
    multi_accept on; 
    #事件驱动模型，select|poll|kqueue|epoll|resig|/dev/poll|eventport
    #use epoll; 
    #单个work进程允许的最大连接数，默认为512
    worker_connections 1024; 
}

#http服务器
http {
    #文件扩展名与文件类型映射表。设定mime类型(邮件支持类型),类型由mime.types文件定义
    #include /usr/local/etc/nginx/conf/mime.types;
    include mime.types; 
    #默认文件类型，默认为text/plain
    default_type application/octet-stream; 

    #取消服务访问日志
    #access_log off;      
    #自定义日志格式
    log_format myFormat '$remote_addr–$remote_user [$time_local] $request $status $body_bytes_sent $http_referer $http_user_agent $http_x_forwarded_for'; 
    #设置访问日志路径和格式。"log/"该路径为nginx日志的相对路径，mac下是/usr/local/var/log/。combined为日志格式的默认值
    access_log log/access.log myFormat; 
    rewrite_log on;

    #允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。（sendfile系统调用不需要将数据拷贝或者映射到应用程序地址空间中去）
    sendfile on; 
    #每个进程每次调用传输数量不能大于设定的值，默认为0，即不设上限。
    sendfile_max_chunk 100k; 

    #连接超时时间，默认为75s，可以在http，server，location块。
    keepalive_timeout 65; 

    #gzip压缩开关
    #gzip on;

    tcp_nodelay on;

    #设定实际的服务器列表
    upstream mysvr1 {    
        server 127.0.0.1:7878;
        server 192.168.10.121:3333 backup; #热备(其它所有的非backup机器down或者忙的时候，请求backup机器))
    }
    upstream mysvr2 {
    #weigth参数表示权值，权值越高被分配到的几率越大
       server 192.168.1.11:80 weight=5;
       server 192.168.1.12:80 weight=1;
       server 192.168.1.13:80 weight=6;
    }
    upstream https-svr {
    #每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题
       ip_hash;
       server 192.168.1.11:90;
       server 192.168.1.12:90;
    }

    #error_page 404 https://www.baidu.com; #错误页

    #HTTP服务器
    #静态资源一般放在nginx所在主机
    server {
        listen 80; #监听HTTP端口
        server_name 127.0.0.1; #监听地址  
        keepalive_requests 120; #单连接请求上限次数
        set $doc_root_dir "/Users/doing/IdeaProjects/edu-front-2.0"; #设置server里全局变量
        #index index.html;  #定义首页索引文件的名称
        location ~*^.+$ { #请求的url过滤，正则匹配，~为区分大小写，~*为不区分大小写。
            root $doc_root_dir; #静态资源根目录
            proxy_pass http://mysvr1; #请求转向“mysvr1”定义的服务器列表
            #deny 127.0.0.1; #拒绝的ip
            #allow 172.18.5.54; #允许的ip              
         } 
    }
    
    #http
    server {
        listen 80;
        server_name www.helloworld.com; #监听基于域名的虚拟主机。可有多个，可以使用正则表达式和通配符
        charset utf-8; #编码格式
        set $static_root_dir "/Users/doing/static";
        location /app1 { #反向代理的路径（和upstream绑定），location后面设置映射的路径 
            proxy_pass http://zp_server1;
        } 
        location /app2 {  
            proxy_pass http://zp_server2;
        } 
        location ~ ^/(images|javascript|js|css|flash|media|static)/ {  #静态文件，nginx自己处理
            root $static_root_dir;
            expires 30d; #静态资源过时间30天
        }
        location ~ /\.ht {  #禁止访问 .htxxx 文件
            deny all;
        }
        location = /do_not_delete.html { #直接简单粗暴的返回状态码及内容文本
            return 200 "hello.";
        }
         
        #指定某些路径使用https访问(使用正则表达式匹配路径+重写uri路径)
        location ~* /http* { #路径匹配规则：如localhost/http、localhost/httpsss等等
        #rewrite只能对域名后边的除去传递的参数外的字符串起作用，例如www.c.com/proxy/html/api/msg?method=1&para=2只能对/proxy/html/api/msg重写。
        #rewrite 规则 定向路径 重写类型;
        #rewrite后面的参数是一个简单的正则。$1代表正则中的第一个()。
        #$host是nginx内置全局变量，代表请求的主机名
        #重写规则permanent表示返回301永久重定向
            rewrite ^/(.*)$ https://$host/$1 permanent;
        }

        #错误处理页面（可选择性配置）
        #error_page 404 /404.html;
        #error_page 500 502 503 504 /50x.html;

        #以下是一些反向代理的配置(可选择性配置)
        #proxy_redirect off;
        #proxy_set_header Host $host; #proxy_set_header用于设置发送到后端服务器的request的请求头
        #proxy_set_header X-Real-IP $remote_addr;
        #proxy_set_header X-Forwarded-For $remote_addr; #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
        #proxy_connect_timeout 90; #nginx跟后端服务器连接超时时间(代理连接超时)
        #proxy_send_timeout 90; #后端服务器数据回传时间(代理发送超时)
        #proxy_read_timeout 90; #连接成功后，后端服务器响应时间(代理接收超时)
        #proxy_buffer_size 4k; #设置代理服务器（nginx）保存用户头信息的缓冲区大小
        #proxy_buffers 4 32k; #proxy_buffers缓冲区，网页平均在32k以下的话，这样设置
        #proxy_busy_buffers_size 64k; #高负荷下缓冲大小（proxy_buffers*2）
        #proxy_temp_file_write_size 64k; #设定缓存文件夹大小，大于这个值，将从upstream服务器传

        #client_max_body_size 10m; #允许客户端请求的最大单文件字节数
        #client_body_buffer_size 128k; #缓冲区代理缓冲用户端请求的最大字节数
    }
         
    #https
    #(1)HTTPS的固定端口号是443，不同于HTTP的80端口；
    #(2)SSL标准需要引入安全证书，所以在 nginx.conf 中你需要指定证书和它对应的 key
    server {
        listen 443;
        server_name  www.hellohttps1.com www.hellohttps2.com;
        set $geek_web_root "/Users/doing/IdeaProjects/backend-geek-web";
        ssl_certificate        /usr/local/etc/nginx/ssl-key/ssl.crt; #ssl证书文件位置(常见证书文件格式为：crt/pem)
        ssl_certificate_key  /usr/local/etc/nginx/ssl-key/ssl.key; #ssl证书key位置
        location /passport {
            send_timeout 90;
            proxy_connect_timeout 50;
            proxy_send_timeout 90;
            proxy_read_timeout 90;
            proxy_pass http://https-svr;
        }
        location ~ ^/(res|lib)/ {
            root $geek_web_root; 
            expires 7d;
            #add_header用于为后端服务器返回的response添加请求头，这里通过add_header实现CROS跨域请求服务器
            add_header Access-Control-Allow-Origin *; 
        }
        #ssl配置参数（选择性配置）
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout 5m;
    }
    
    #配置访问控制：每个IP一秒钟只处理一个请求，超出的请求会被delayed
    #语法：limit_req_zone  $session_variable  zone=name:size  rate=rate (为session会话状态分配一个大小为size的内存存储区，限制了每秒（分、小时）只接受rate个IP的频率)
    limit_req_zone  $binary_remote_addr zone=req_one:10m    rate=1r/s nodelay;
    location /pay {
        proxy_set_header Host $http_host;
        proxy_set_header X-Real_IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        #访问控制：limit_req zone=name [burst=number] [nodelay];
        limit_req zone=req_one burst=5; #burst=5表示超出的请求(被delayed)如果超过5个，那些请求会被终止（默认返回503）
        proxy_pass http://mysvr1;
    }

    #可以把子配置文件放到/usr/local/etc/nginx/servers/路径下，通过include引入
    include /usr/local/etc/nginx/servers/*.conf;
}    
```



# 内置全局变量

```bash
$args ：这个变量等于请求行中的参数，同$query_string
$content_length ： 请求头中的Content-length字段。
$content_type ： 请求头中的Content-Type字段。
$document_root ： 当前请求在root指令中指定的值。
$host ： 请求主机头字段，否则为服务器名称。
$http_user_agent ： 客户端agent信息
$http_cookie ： 客户端cookie信息
$limit_rate ： 这个变量可以限制连接速率。
$request_method ： 客户端请求的动作，通常为GET或POST。
$remote_addr ： 客户端的IP地址。
$remote_port ： 客户端的端口。
$remote_user ： 已经经过Auth Basic Module验证的用户名。
$request_filename ： 当前请求的文件路径，由root或alias指令与URI请求生成。
$scheme ： HTTP方法（如http，https）。
$server_protocol ： 请求使用的协议，通常是HTTP/1.0或HTTP/1.1。
$server_addr ： 服务器地址，在完成一次系统调用后可以确定这个值。
$server_name ： 服务器名称。
$server_port ： 请求到达服务器的端口号。
$request_uri ： 包含请求参数的原始URI，不包含主机名，如：”/foo/bar.php?arg=baz”。
$uri ： 不带请求参数的当前URI，$uri不包含主机名，如”/foo/bar.html”。
$document_uri ： 与$uri相同。
```









