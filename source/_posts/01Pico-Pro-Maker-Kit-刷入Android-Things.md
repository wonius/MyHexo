---
title: 01Pico Pro Maker Kit 刷入Android-Things
date: 2018-01-06 11:39:12
tags: Android-Things
categories: IoT
keywords: 
- Android-Things
- Pico Pro Maker Kit
description: Pico Pro Maker Kit 刷入Android-Things
---

## 背景

​	2017年底，参加了GDD上海活动，初次接触了Android Things，并且拿到了一块开发板，Pico Pro Maker Kit，拿回来后有事一直闲置。最近有时间掏出来研究下。

{% asset_img 1.JPG 未开苞 %}

## 组装

​	组装很简单，按照官网教程做，很快就组装好了。

​	https://developer.android.google.cn/things/hardware/imx7d-kit.html#install-at

​	不过我拿到的是阉割版，没有Rainbow HAT，差评...

{% asset_img 2.JPG 组装完成 %}

## 下载Android-Things

​	到官网下载Android things setup utility。可是官网上的链接一直无法下载，后来联系了google里的开发人员，发到我的邮箱才拿到。已经上传到百度网盘，可以直接下载。（之前在网上有搜到某同学上传到csdn，下载还需要积分，没积分的宝宝心里苦/(ㄒoㄒ)/~~）

链接: https://pan.baidu.com/s/1dgmIvk 密码: rsps

## 配置fastboot

​	在写入镜像之前，需要确保已安装SDK（我装了Android Studio，内置SDK。你也可以单独装）。将fastboot所在目录加入到环境变量中，不然无法成功执行fastboot命令。

{% asset_img 6.png 找不到fastboot命令 %}

查找fastboot文件目录，并确保fastboot有执行权限，并且属主正确：

{% asset_img 4.png fastboot %}

加入环境变量：

```
//我安装了ohmyzsh插件，所以需要编辑.zshrc。同学们根据实际配置修改
vim ~/.zshrc

//文件末尾中加入
export PATH=$PATH:/Users/woniu/Library/Android/sdk/platform-tools

//别忘了编译
source ~/.zshrc

//检测是否可用
fastboot -h
```



## 写入Android-Things

​	下载Android things setup utility成功之后，解压，运行android-things-setup-utility-macos，按照提示选择，在线下载镜像并且刷入（需要科学上网。没有条件的同学也可以到我的百度云下载iot_imx7d_pico镜像，手动安装。链接: https://pan.baidu.com/s/1hticxNI 密码: 4rvc）。

{% asset_img 3.png 找不到fastboot命令 %}

从上图可以看到，最后报错了，原因是找不到fastboot命令。当我配置好fastboot后重新在线安装，一直无法下载。只好下载镜像手动安装。

下载iot_imx7d_pico后，解压，到该目录下执行：

```
./flash-all.sh		//Win系统下执行flash-all.bat
```

之后静待安装，完成后会自动重启，就可以了：

{% asset_img 5.jpeg 成功 %}

