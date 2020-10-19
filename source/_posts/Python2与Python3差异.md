---
title: Python2与Python3差异
copyright: true
date: 2018-05-23 16:31:59
tags: Python
categories: Python
keywords: Python
description: Python2与Python3差异
---

## 方法

| 方法             | Python2                 | Python3                                  |
| -------------- | ----------------------- | ---------------------------------------- |
| file.write()   | file.write(str)         | file.write(str.encode()) # 需转为byte       |
| self.cf.read() | self.cf.read(file_path) | self.cf.read(file_path, encoding="") #需指定编码 |
|                |                         |                                          |

