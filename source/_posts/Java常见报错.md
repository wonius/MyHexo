---
title: Java常见报错
date: 2018-03-06 20:07:22
tags: Java
categories: Java
keywords: Java
description: Java常见报错
copyright: true
---

### List(Arrays.asList)后add或remove出现UnsupportedOperationException

当使用Arrays.asList()，将Array转换为List后，如果使用List.add()、List.remove()方法，会报改错。

根本原因是转换后创建的对象是一个ArrayList对象，该对象集成了AbstractList，没有实现add、remove方法，所以使用的时候会报错。

改正：

new ArrayList<>(Arrays.asList());
