---
title: pip2与pip3无法并存共用
copyright: true
date: 2018-05-23 10:23:24
tags: Python
categories: Python
keywords: 
- pip
- Python
description: 解决pip2与pip3无法并存共用问题
---

## 环境

OS：

​	MacOS 10.13.4

Python：

​	python2.7(系统自带)：/System/Library/Frameworks/Python.framework/Versions/2.7

​	python3.6.5(后来安装)：/usr/local/Cellar/python/3.6.5/

pip：

​	pip2：/usr/local/bin/pip

​	pip3：/usr/local/bin/pip3

## 升级

​	当我安装python3.6.5之后，python3中自带pip3，且将原来装的pip覆盖，导致pip无法使用。虽然不用pip命令也可以使用pip功能（`python -m pip install XXXX`），可以通过重新安装pip方法，使pip生效。

```
sudo python -m pip install --upgrade pip --force-reinstall
# 查看pip信息
pip -V

# 如果pip3也有问题，重新安装一遍pip3
sudo python3 -m pip install --upgrade pip --force-reinstall
# 查看pip3信息
pip3 -V
```

​	