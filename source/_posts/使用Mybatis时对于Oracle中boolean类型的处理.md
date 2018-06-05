---
title: 使用Mybatis时，对于Oracle中boolean类型的处理
date: 2017-07-15 22:35:41
tags: 
- Mybatis
- Oracle
categories: DB
keywords: 
- Mybatis
- Oracle
description: Mybatis+Oracle，对boolean类型的处理
copyright: true
---

# DB为Oracle时，参数中存在boolean类型
&emsp;&emsp;Oracle不支持boolean类型，针对此类型，Oracle的驱动包中做了转换处理，将true转换为 ***数字***  1，false转换为 ***数字***  0。

&emsp;&emsp;Mybatis解析sqlmapper文件时，调用TypeHandler类中的方法对类型进行处理，针对boolean类型的实现类为BooleanTypeHandler，如下，此类包含在ojdbc包中。其中setNonNullParameter方法为传参时，对入参的处理。

{% asset_img BooleanTypeHandler.png BooleanTypeHandler类 %}

&emsp;&emsp;setBoolean的具体实现。我们可以看到，boolean类型最后转换为Int类型。

{% asset_img setBooleanInternal.png setBoolean方法的实现%}

&emsp;&emsp;因此，在做insert、update操作时，用decode进行处理，即可将true/false转换成自己想要的值。我通常用Y/N表示状态。

```
decode(#{enabled}, 1, 'Y', 'N')
```


# DB为Oracle时，将查询结果转换为boolean类型
&emsp;&emsp;同样，由TypeHandler作为入口，查看Mybatis的处理方式。针对不同情况，有三种处理方法，根据入参大体可以猜到每个方法的作用，此处不做展开。挑选一个方法深入，最终找到getBoolean的实现。

{% asset_img TypeHandler.png TypeHandler类%}

&emsp;&emsp;下图中getBoolean方法属于ResultSet类，在JDK中rt.jar包中（我的环境为JDK1.8）。根据注释我们可以知道，如果值为如下类型的 0，返回结果为false；如果值为如下类型的1，返回true。

{% asset_img getBoolean.png getBoolean方法的实现%}

&emsp;&emsp;所以当你希望将ResultSet中的某个属性转换为boolean类型时，可以使用如下方式。enabled为字段名称。


```
decode(enabled, 'Y', 1, 'N', 0) enabled
```
