---
title: Websocket+Stomp+RabbitMQ应用
date: 2017-08-02 20:36:08
tags: WebSocket
categories: 积累
keywords: 
- WebSocket
- Stomp
- RabbitMQ
description: 通过WebSocket+Stomp，订阅RabbitMQ消息队列
---

## 背景

&emsp;&emsp;最近开发中遇到实时接收关联系统信息的功能，关联系统通过RabbitMQ消息队列发布消息，之前还在想通过浏览器链接自家服务器，然后自家服务器订阅关联系统队列的架构。后来查了一下，不用这么麻烦，直接通过WebSocket+Stomp方式，即可实现浏览器直接订阅关联系统消息队列。
