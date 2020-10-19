---
title: Mybatis使用association，结果返回数组
date: 2017-10-14 21:04:57
tags: Mybatis
categories: Backend
keywords: 
- Mybatis
- association
description: 当使用Mybatis中的association标签时，发现会将返回对象以数组的形式返回
copyright: true
---

关于association、collection标签网上很多帖子，这里就不赘述，直接讲问题。

### 问题

我DAO接口返回的数据格式为 `List<Map<String, Object>>`，Map中的数据对象为如下，Book是一个实体。

```
	<resultMap type="HashMap" id="PushInfo">
        <result column="id" property="id"/>
        <result column="pushDate" property="pushDate"/>
        <association property="book" select="selectBookById" column="book_id" javaType="Book"/>
    </resultMap>
    
    <select id="selectBookById" resultType="Book">
		SELECT book.*,user.nickName AS `createUser.nickName` FROM book 
		LEFT JOIN `user` ON user.id = book.createUser
		WHERE book.id = #{bookId}
	</select>
```

实际返回的结果，出现了格式不一致的问题:

```
	{
        "pushDate": 1494645459000,
        "book": {			//该处，book是实体对象，是正常的
            "status": null,
            "createUser": null,
            "tags": "[唐家三少, 斗罗大陆, 玄幻, 网络小说, 小说, 三少的书，很经典, 奇幻, 魔幻小说]",
            "imgUrl": "https://img3.doubanio.com/lpic/s6847563.jpg",
            "enable": true,
            "author": "唐家三少",
            "doubanScore": 7.5,
            "createDate": 1420038859000,
            "name": "斗罗大陆（全14卷）",
            "content": "",
            "book_id": 3249
        }
    },
    {
        "pushDate": 1494571741000,
        "book": [			//此时，却变成了book实体的数组对象
            {
                "status": null,
                "createUser": null,
                "tags": "[唐家三少, 斗罗大陆, 玄幻, 网络小说, 小说, 三少的书，很经典, 奇幻, 魔幻小说]",
                "imgUrl": "https://img3.doubanio.com/lpic/s6847563.jpg",
                "enable": true,
                "author": "唐家三少",
                "doubanScore": 7.5,
                "createDate": 1420038859000,
                "name": "斗罗大陆（全14卷）",
                "content": "",
                "book_id": 3249
            }
        ]
    }
···········
```



### 解决方案

新建了BookPush实体类，指定book类型。

```
	<resultMap type="BookPush" id="PushInfo">
        <result column="id" property="id"/>
        <result column="pushDate" property="pushDate"/>
        <association property="book" select="selectBookById" column="book_id" javaType="Book"/>
    </resultMap>
```



### 分析

仔细核查数据可以发现，当父查询返回的book_id有重复时，第二个book_id查出来的数据，返回的一定是一个数组。比如上面，book_id都为3249，第一个结果为实体，第二个结果为数组。

我猜测association标签在执行时，会将同一个事务中的数据缓存。先在缓存中查，发现有一个实体，所以就创建了数组，然后再执行sql，查出来的结果与缓存中的一样，所以数组中只有一个元素。

但是感觉跟数据类型也有关系

以后有空看看这部分的源码
