---
title: Vimium快捷键
copyright: true
date: 2018-05-03 17:17:22
tags: Chrome
categories: Chrome
keywords: Vimium
description: Vimium快捷键一览
---

## 常用快捷键

```
向下/上/左/右移动  j/k/h/l
向下/上跳动  d/u
回到顶/尾部  gg/G
窗口打开模式 本窗口/新窗口 f/F
查找历史记录+书签   o/O
关闭/恢复标签 x/X
查找书签  b/B（当前/新窗口打开）
选择左/右标签 J/K
搜索剪贴板关键字 在当前/新窗口  p/P
跳转到当前url上一级/最高级 gu/gU
创建/查看标签页  t/T
将焦点聚集在第一个输入框  gi  (2gi就是第二个输入框)
刷新 r
新标签中打开多个链接   <a-f> 即：alt+f
开/关静音  <a-m>即：alt+m
固定标签栏 <a-p>即 alt+p
上一个标签  ^
```

## 不常用快捷键

```
查找（不支持中文）  /
    向下/上查找结果  n/N  (回车后直接打开链接，不用再使用f/F定位)
复制当前链接 yy
新模式 i
查看源码 gs
查看所以快捷键 ?
编辑当前地址栏 g+e/E   并在当前/新窗口中打开
复制当前标签页  yt
移动当前标签到左/右侧边  <</>>
滚动到页面最左/右边（在有滚动条下才有效果） zH/zL
插入模式  i（可以屏蔽掉vimium快捷键，使其不和网页默认快捷键冲突）
将标签页移动到新窗口  W
创建新标记（可创建多个   m 使用方法
      设置当前/全局滚动条位置   m+小/大写字母
      跳转到设置的滚动位置   ~+字母
切换到复制模式 v
```

## 其他技巧

```
作者：小佐
链接：https://www.zhihu.com/question/23483616/answer/246787555
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

焦点切换
    从地址栏切换回页面  tag
翻页
    下/上一页  ]]  [[  部分网站不支持  可以使用 Shortcut Manager 插件代替
    下一页代码：
        function next(){
            var a=$(".cur").next().find("a").attr("href");
             window.location.assign(a);
        }
        next();
    上一页代码：
        function after(){
            var a=$(".cur").prev().find("a").attr("href");
             window.location.assign(a);
        }
        after();
自定义搜索引擎
    设置：
        请查看选项：Custom search engines
    使用：
        按b/B打开搜索框，输入搜索引擎关键字+空格
    扩展：
        计算功能
            g (1+2)*3-4=     进入google搜索模式直接输入公式后面加"="号
打开chrome系统页面
    chrome设置页面
        about:setting
    扩展程序
        about:extensions
    历史(history)，下载(downloads)，书签(bookmarks),建议使用chrome默认快捷键代替
        ctrl+h,ctrl+j,ctrl+shift+o
关于<a-p>,<c-e>
    是emacs中的表示方法，分别指alt+p,ctrl+e
快速定位+复制文本：alt+f,v,hjkl,y四步曲
    alt+f 搜索指定关键字，并定位到起始点
    再按v切换到复制模式
    再使用hjkl控制方向选择范围
    最后y复制
删除/修改命令
    删除指定命令
        unmap j   删除命令j
        unmapAll  删除所有命令
    修改命令（后面的命令参数可以在选择里面打开 show available commands找到）
        map a LinkHints.activateMode  把a定义原来f的功能
        map f scrollPageDown   把f定义成原来d的功能
本地文件中使用vimium
    打开chrome插件设置页面，勾选"允许访问文件网址"
```

