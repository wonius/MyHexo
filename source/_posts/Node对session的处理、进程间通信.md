---
title: Node对session的处理、进程间通信
date: 2017-08-19 12:52:53
tags: Node
categories: Frontend
keywords: Node
description: Node服务器对session的处理、进程间通信
copyright: true
---

## 对于session的处理

​	最近项目中有用到Node，也在学习。开发过程中发现，Node对于session的处理方式与普通服务器不同。

1. Node服务器收到请求后，会为本次请求创建session，但是如果这次请求中没有对session的处理（放值等操作），在请求返回时不会做set-cookie操作，该次请求不会生成cookie。所以重复调用该请求每次都会生成一个新的session；
2. 请求处理函数中对session有操作，返回时有set-cookie，生成cookie，下次请求时不会生成新的session；


## 进程通信

​	Node开始时是单线程，为了有效利用资源，后来增加了child_process可以多进程。后来又封装了cluster模块，更好的处理多进程。进程间通信主要依赖于监听。

```
var cluster = require('cluster');
var numCPUs = require('os').cpus().length;

//主进程
if (cluster.isMaster) {
    console.log('[master] ' + "start master...");

  //根据CPU数量创建子进程
    for (var i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
  
  //cluster监听
    cluster.on('listening', function (worker, address) {
        console.log('[master] ' + 'listening: worker' + worker.id + ',pid:' + worker.process.pid + ', Address:' + address.address + ":" + address.port);
    });
  
  
  Object.keys(cluster.workers).forEach(function (id) {
    	//监听所有的worker
        cluster.workers[id].on('message', function (msg) {
            console.log('[master] ' + 'received msg:' + msg + 'from worker' + id);
        });
    
    	//向子进程发送信息
    	cluster.workers[id].send('[master] ' + 'send msg ' + i + ' to worker' + worker.id);
    });
  
} else if (cluster.isWorker) {	//子进程

  //向父进程发送数据
    process.send(data);
  //监听数据
  	process.on('message', function(data) {
    });
  //子进程都监听3000端口
    http.createServer(app).listen(3000) ;
}
```

**cluster对象**
cluster的各种属性和函数

- cluster.setttings:配置集群参数对象
- cluster.isMaster:判断是不是master节点
- cluster.isWorker:判断是不是worker节点
- Event: 'fork': 监听创建worker进程事件
- Event: 'online': 监听worker创建成功事件
- Event: 'listening': 监听worker向master状态事件
- Event: 'disconnect': 监听worker断线事件
- Event: 'exit': 监听worker退出事件
- Event: 'setup': 监听setupMaster事件
- cluster.setupMaster([settings]): 设置集群参数
- cluster.fork([env]): 创建worker进程
- cluster.disconnect([callback]): 关闭worket进程
- cluster.worker: 获得当前的worker对象
- cluster.workers: 获得集群中所有存活的worker对象

**worker对象**
worker的各种属性和函数：可以通过cluster.workers, cluster.worket获得。

- worker.id: 进程ID号
- worker.process: ChildProcess对象
- worker.suicide: 在disconnect()后，判断worker是否自杀
- worker.send(message, [sendHandle]): master给worker发送消息。注：worker给发master发送消息要用process.send(message)
- worker.kill([signal='SIGTERM']): 杀死指定的worker，别名destory()
- worker.disconnect(): 断开worker连接，让worker自杀
- Event: 'message': 监听master和worker的message事件
- Event: 'online': 监听指定的worker创建成功事件
- Event: 'listening': 监听master向worker状态事件
- Event: 'disconnect': 监听worker断线事件
- Event: 'exit': 监听worker退出事件

