---
title: MacOS安装Matlab
copyright: true
date: 2018-05-20 23:45:43
tags: matlab
categories: 工具
keywords: Matlab
description: MacOS安装Matlab_R2015b
---

## 下载

​	百度网盘链接:

​	安装包：https://pan.baidu.com/s/1Y1llTPxRa55ojXotTkg6eQ 密码: wchd

​	破解补丁包：https://pan.baidu.com/s/1iTrHoTXmhut-nedNSf4RZg  密码:b7oz

​	如果下载速度过慢，可参考本博客搭建Aria2，享受会员版的下载速度。

##安装

​	下载后是一个iso文件，打开，断开网络连接，双击InstallForMacOSX.app，点下一步。

​	选择“使用文件安装秘钥”，点击“下一步“，同意条款，然后将`5442240402238172080830933`拷贝到许可证秘钥窗口，一直”下一步“，等待安装完成。

​	用破解补丁包中的libmwservices.dylib替换掉/Applications/MATLAB_R2015b/bin/maci64/libmwservices.dylib。

​	打开Matlab_R2015b，选择离线激活方式，加载破解补丁包中的/license.lib，成功。