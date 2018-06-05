---
title: IE8遇到的坑
date: 2017-08-23 22:06:26
tags: IE8
categories: BS
keywords: IE8
description: IE8遇到的一些坑
copyright: true
---

## new Date()

​	当new Date()时，IE8与IE11产生的结果不同。

```
//IE8，date1格式为‘2017年8月23日’。当将该参数传递到rest接口时，无法绑定到date类型
//另外，使用oracle的to_date()方法时，to_date('', 'yyyy"年"MM"月"dd"日"')
var date1 = new Date();

//IE11，date2格式为‘2017/8/23’
var date2 =new Date();

//为了兼容IE8和IE11，需在前端对日期做处理
var date = new Date();
var today = date.getFullYear()+"-"+date.getMonth()+"-"+date.getDay();
```

## for(var i in obj)

​	IE8支持JS版本较低，Array.prototype中的函数无法使用。比如forEach、indexOf等。一般为了兼容，使在IE8下也可以使用这些函数，会重写Array.prototype，加上这些函数。

```javascript
if(!Array.prototype.forEach) {
  Array.prototype.forEach = function() {
      
  }
}
```

​	但是，对于对象原型的操作，是很危险的事情，严重会导致导致原型链断裂。不过这次讨论的不是这个问题。

​	当IE8解析for in 方法时，会将forEach等添加到prototype上的方法当做变量，一起循环。所以循环体里如果对变量进行了操作，会导致报错。

```javascript
for(var i in params) {
    console.log(params[i].length);
}
```

​	解决方法：

1. 在使用for..in时，使用 hasOwnProperty() 对变量进行判断：

   ```
   for(var i in params) {
     if(params.hasOwnProperty(params[i]))
       console.log(params[i].length);
   }
   ```

   ​

2. for循环的另一种写法：

   ```
   for(var i = 0; i < params.length; i++) {
       console.log(params[i].length);
   }
   ```

3. 使用框架封装好的接口，_.forEach，如果不想引入框架，可以自己写一个forEach函数。

