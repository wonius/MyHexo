---
title: Hexo中的Next主题常用设置
date: 2017-07-21 00:13:50
tags: Next
categories: 笔记
keywords: 
- Hexo
- Next
description: Hexo中的Next主题常用设置
---

## 设置favicon.ico（网站logo）
&emsp;&emsp;使用主题为next，按照主题内_config.yml所说，将icon放到hexo-site/source/目录下，没有生效。查了些资料，终于好了，设置如下：

1. 找一副自己中意的图片，尽量简单，因为会缩成很小。利用[在线工具](http://tool.lu/favicon/)制作icon，保存。我的大小是32*32，加载时大多时候很流畅，有时也会转啊转；
2. 将保存的favicon.ico移动至hexo-site/themes/next/source/images目录下；
3. 修改next配置文件_config.yml，如下：
```
favicon: images/favicon.ico
```
4. hexo g && hexo s，就可以在本地看到效果了。调试满意后，hexo d发布；

## 利用Leancloud实现阅读统计，请求异常401
&emsp;&emsp;如何配置leancloud在此不做赘述，next作者文档中写的已经很清晰了，可以先移步到[NEXT](http://theme-next.iissnan.com/getting-started.html#third-party-services)看看。笔者的情况是，博客在github上，在万网买了个域名，绑定到github。

&emsp;&emsp;配置完成之后，发现阅读统计不能正常使用，开始以为缓存原因，过了两天，还是没好，打算解决这个问题。

&emsp;&emsp;分析过程：

1. 接口报错401：Unauthorized，再次检查了一下AppKey、Web安全域名配置，都正常。比较纳闷，开始研究。
2. 用chrome看了下counter的请求，Request URL竟然是<https://api.us.leancloud.cn/****>。看起来比较诡异，us不是美国吗。难道访问的url错了？
3. 重新登录leancloud，发现域名是<https://leancloud.cn/>，没有us。新开tab页，访问<https://us.leancloud.cn/>，也可以成功登录。登录后仔细对比，才发现原来leancloud右上角有个选项，可以选择国内节点、美国节点。两个节点中的应用是相互隔离的。我的next中配置的是国内节点中应用的配置，请求到美国节点认证，所以认证不通过。明白之后，我在美国节点中也创建了应用，把next中的相关配置更新，部署，果然可以正常显示。

&emsp;&emsp;个人猜测，由于github服务器在美国，所以当请求leancloud时，请求被分发到了美国节点上的服务器上。

补充：后来更新了NexT代码，又必须配置成国内节点才可以访问。

## 增加关键字、摘要
&emsp;&emsp;建议通过在模板中增加属性的方式，添加关键字、摘要。

1. 修改next/_config.yml配置文件:
```
  auto_excerpt:
  //开启摘要
  enable: true
  //默认文章的前150个字符作为摘要
  length: 150
```
2. 修改hexo-site/scaffolds/post.md文件，增加keywords、description。
```
---
title: {{ title }}
date: {{ date }}
tags:
categories: 
keywords: 
//摘要，填写后显示该字段的值。没有该地段，自动取文章前150个字符作为摘要
description: 
---


```

## 修改文章宽度
&emsp;&emsp; 修改/next/source/css/_custom/custom.styl。该文件提供给用户配置自定义的样式，用户尽量避免对源代码的改动，以免牵一发而动全身。
针对Mist、Muse风格，可以用如下配置
```
// 修改成你期望的宽度
$content-desktop = 1080px

// 当视窗超过 1600px 后的宽度
$content-desktop-large = 1200px
```
如果是Pisces风格，则需要
```
header{ width: 90%; }
.container .main-inner { width: 90%; }
.content-wrap { width: calc(100% - 260px); }
```

## 新建文章时同时建立同名目录，保存图片

```
//编辑hexo目录下的_config.yml，修改如下配置
post_asset_folder:true

//新建文件
hexo new 'helloworld'

//将图片移动到helloworld目录中，并且在md中引用
{% asset_img 2.JPG 这是描述 %}
```

