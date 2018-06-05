---
title: Sql优化
date: 2017-08-13 15:39:31
tags: Sql
categories: DB
keywords: Sql优化
description: 通用的sql优化小技巧
copyright: true
---

## 什么情况下可以全表扫

公司这边的要求是，Sql的查询数据小于50M时，可以全表扫。

假如一条数据大小为100B，50M对应的数据量为：

$50*1024*1024/100=524288$, 约50w条数据。所以当表中数据量小于50w时，可以全表扫。



## Oracle分页

众所周知，Oracle分页的写法比较麻烦，需要用嵌套查询的方式。举个🌰：

```
Select * from 
(
	select rownum rowno, t.* 
	from
  	(select id,
     		name,
     		salary
     	 from worker
     	 order by name desc) t		//如果分页时需要排序，一定要在子查询里排序完成后，再分页
  	where rownum <= 10				//< 一定在子查询中。因为rownum是查询出数据之后再给rownum赋值。否则从第2页开始查询结果都为空
) where rowno >0;
```

另外还有一种写法，使用between：

```
select rownum, w.* 
from worker w
where 
rownum between 1 and 10;
```

这种格式更加简单，不过效率可能会有不同。具体还是要看执行计划，选择最优。



## 批量更新时，如果参数不固定，造成硬解析

比如我们有个场景，需要根据id批量更新某个值。一般都是会将id放到list中，将list作为参数传入。

```
update worker
set status = "Y"
where id in
<iterator open="(" close=")" conjunction=",">
	#list[]#
</iterator>
```

因为入参是list，长度不固定，生成的sql也会不同，数据库在执行时每次都要生成新的执行计划，导致硬解析。

针对这种情况，我能想到的有如下两种方式处理：

1. **将list长度固定**

   将大批量分成若干小批次，比如每批次更新100条，list定长为100，当最后的批次长度不够100时，后面的数据设置为空。这样生成的sql就是固定的，执行计划相同。

2. **将整个sql循环**

   将整个sql循环，而不是参数。例如入参是有100个参数的list，循环创建出100条update语句，参数固定为一个，形式如下：

   ```
   <foreach collection=list item="item" index="index" open="begin" close=";end;" separator=";">
   update worker 
   set status = "Y"
   where id = #{item.id}
   </foreach>
   ```

   ​
