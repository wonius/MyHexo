---
title: AWS免费EC2创建记录
date: 2018-03-17 14:30:26
tags: AWS
categories: 云服务器
keywords: AWS
description: AWS免费EC2创建操作记录
copyright: true
---

## 注册AWS账号

如果没有AWS账号，请先注册。注意AWS账号与Amazon商城是两套账号系统，不能共用。[AWS主页](https://aws.amazon.com/cn/)

## 选择机房

选择加州机房，速度快：

{% asset_img 1主机.jpg %}

## 选择系统镜像

注意选择免费套餐：

{% asset_img 2镜像.jpg %}

## 选择实例

同样，选择免费套餐：

{% asset_img 3实例.jpg %}

## 实例配置

默认配置就可以：

{% asset_img 4配置.jpg %}

## 设置存储

默认5G，可以调整到30G：

{% asset_img 5存储.jpg %}

## 设置标签

即实例标签，用来区分不同实例。这里我添加了Name标签，并为该实例命名：

{% asset_img 6标签.jpg %}

## 设置安全组

设置开放的接口，将常用的ssh、http、https协议开放：

{% asset_img 7安全组.jpg %}

## 设置秘钥对

设置秘钥对，并下载。使用ssh访问服务器时用于校验：

{% asset_img 8AwsKeyPair.jpg %}

## SSH访问

创建完成后，查看创建好的实例，系统会介绍如何通过ssh访问服务器。将上一步下载好的pem文件保存到本地，设置权限，并在该目录下使用命令进行链接：

{% asset_img 9ssh.jpg %}

每次都要到pem所在目录访问，比较麻烦。可以将pem加入到ssh中，就可以在任何目录下访问了(别忘了先将pem文件权限修改为400)：

{% asset_img 10ssh-add.jpg %}

连接服务器：

{% asset_img 11ssh2.jpg %}
