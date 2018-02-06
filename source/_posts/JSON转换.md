---
title: JSON转换
date: 2017-08-19 21:20:31
tags: JSON
categories: 积累
keywords: JSON
description: 
---

## JSON.parse(data)转换

1. 当data为 ‘undefined’ 时，转换异常。为 ‘null’ 时，转换正常；
2. 如果data不是key、value形式的字符串，是 ‘1234’ 这种只有一个值的字符串，也可以转换，转换后还是原值；

