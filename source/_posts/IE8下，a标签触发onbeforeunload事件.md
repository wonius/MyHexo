---
title: IE8下，a标签触发onbeforeunload事件
date: 2017-08-03 22:06:26
tags: onbeforeunload
categories: BS
keywords: 
- IE8
- onbeforeunload
description: IE8下，a标签触发onbeforeunload事件
---

## 背景

&emsp;&emsp;今天开发过程中，遇到个奇怪现象。我们系统针对onbeforeunload事件做了处理，当该事件触发时弹框提示，由用户确认退出。当点击a标签时，IE8下触发了该事件。IE11与Chrome都没有问题。好吧，又是IE的坑。


## 分析
&emsp;&emsp;网上搜索一番，原来点击a标签会以此触发如下事件

onclick->window.onbeforeunload->href

所以解决这个问题，在onclick的回调函数末尾，加 **return false;** ，阻止事件传递到onbeforeunload。

另外还有网友说，**href=“###”** 也可以解决该问题


## 补充
根据 MSDN 中描述，IE 的 onbeforeunload 事件可由以下这些条件触发：

- 关闭当前浏览器窗口。
- 导航到另一个进入一个新的地址或选择一个喜欢的位置。
- 单击后退，前进，刷新，或主页按钮。
- 点击一个链接到新页面。
- 调用 超链接的 click 方法。
- 调用 document.write 方法。
- 调用 document.open 方法。
- 调用 document.close 方法。
- 调用 window.close 方法。
- 调用 window.open 方法，窗口名称设置值为 _self。
- 调用 window.navigate 或 NavigateAndFind 方法。
- 调用 location.replace 方法。
- 调用 location.reload 方法。
- 指定一个 location.href 属性的新值。
- 使用 submit 按键提交表单，或调用 form.submit 方法。




<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品由<a xmlns:cc="http://creativecommons.org/ns#" href="http://wonius.top/" property="cc:attributionName" rel="cc:attributionURL">Gavin</a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。