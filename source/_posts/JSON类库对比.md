---
title: JSON类库对比
date: 2017-08-19 21:20:31
tags: JSON
categories: Java
keywords: JSON
description: 整理了下常用的几个JSON类库，并做对比，帮助大家进行选型
copyright: true
---

# json-lib（性能差）

json-lib最开始的也是应用最广泛的json解析工具，json-lib 不好的地方确实是依赖于很多第三方包，
包括commons-beanutils.jar，commons-collections-3.2.jar，commons-lang-2.6.jar，commons-logging-1.1.1.jar，ezmorph-1.0.6.jar，
对于复杂类型的转换，json-lib对于json转换成bean还有缺陷，比如一个类里面会出现另一个类的list或者map集合，json-lib从json到bean的转换就会出现问题。
json-lib在功能和性能上面都不能满足现在互联网化的需求。

# Jackson

相比json-lib框架，Jackson所依赖的jar包较少，简单易用并且性能也要相对高些。
而且Jackson社区相对比较活跃，更新速度也比较快。
Jackson对于复杂类型的json转换bean会出现问题，一些集合Map，List的转换出现问题。
Jackson对于复杂类型的bean转换Json，转换的json格式不是标准的Json格式

# Gson

Gson是目前功能最全的Json解析神器，Gson当初是为因应Google公司内部需求而由Google自行研发而来，
但自从在2008年五月公开发布第一版后已被许多公司或用户应用。
Gson的应用主要为toJson与fromJson两个转换函数，无依赖，不需要例外额外的jar，能够直接跑在JDK上。
而在使用这种对象转换之前需先创建好对象的类型以及其成员才能成功的将JSON字符串成功转换成相对应的对象。
类里面只要有get和set方法，Gson完全可以将复杂类型的json到bean或bean到json的转换，是JSON解析的神器。
Gson在功能上面无可挑剔，但是性能上面比FastJson有所差距。

# fastjson

Fastjson是一个Java语言编写的高性能的JSON处理器,由阿里巴巴公司开发。
无依赖，不需要例外额外的jar，能够直接跑在JDK上。
FastJson在复杂类型的Bean转换Json上会出现一些问题，可能会出现引用的类型，导致Json转换出错，需要制定引用。
FastJson采用独创的算法，将parse的速度提升到极致，超过所有json库。

[fastjson这么快老外为啥还是热衷 jackson?](https://www.zhihu.com/question/44199956)

# 比较

选择一个合适的JSON库要从多个方面进行考虑：

1. 字符串解析成JSON性能
2. 字符串解析成JavaBean性能
3. JavaBean构造JSON性能
4. 集合构造JSON性能
5. 易用性

## 性能

1. 字符串解析成JavaBean（反序列化）：当数据量较少时首选FastJson，数据量较大使用Jackson。但是Jackson无法堆一个对象集合进行解析，只能转成一个Map集合，这点Gson和FastJson处理的比较好。
2. 字符串解析成JSON：当数据量较少时首选FastJson，数据量较大使用Jackson。
3. JavaBean构造JSON（序列化）：当数据量较少时选择Gson，数据量较大可使用Jackson。
4. 集合构造JSON（序列化）：首先Jackson，其次Fastjson。

## 易用性

1. FastJson的API设计的最简单，最方便使用，直接使用JSON的两个静态方法即可完成四种操作；
2. Gson和Jackson都需要new一个对象，虽然这个对象可以复用，但是在实际使用过程中还需要用一个全局变量来保存改变量，同时API设计的也不是很好理解，对于FastJson来说复杂的API是因为他支持流式解析，适合对JSON进行大量且复杂的操作，但是实际应用中对于JSON的操作都是简单的解析成JavaBean，然后JavaBean序列化成JSON字符串即可，复杂的操作很少；

# 结论

综上4种Json技术的比较，在项目选型的时候，如果性能没有什么要求，可以使用Jackson；

如果性能上有要求，可以Jackson（或Gson）与FastJson两种并行使用，使用Jackson（或Gson）将bean转换成json，使用FastJson将Json转换Bean。

# one more thing

在使用JS的JSON.parse(data)转换时：

1. 当data为 ‘undefined’ 时，转换异常。为 ‘null’ 时，转换正常；
2. 如果data不是key、value形式的字符串，是 ‘1234’ 这种只有一个值的字符串，也可以转换，转换后还是原值；