---
title: Git学习笔记
date: 2017-07-14 00:06:02
tags: Git
categories: 笔记
keywords: Git
description: Git常用命令
---

## 更新代码时，本地冲突无法更新
错误信息：
> error: Your local changes to the following files would be overwritten by merge:
>
> laravel/app/Services/ExpressService.php
>
> Please commit your changes or stash them before you merge.

解决办法：

```
git stash

git pull

git stash pop
```

如果希望更新时覆盖本地文件，命令如下：

```
git reset --hard
git pull
```



## 回退到某个历史版本

1. 首先查看要回退到哪个历史版本id：

   ```Git
   git log
   ```

2. 回退。比如id为784dd834b250d634a7686f28a7c323d5c6402e90

   ```
   //该回退动作会将本地所有改动回退，所以一定要记得stash。
   git reset --hard 784dd834b250d634a7686f28a7c323d5c6402e90
   ```

3. 如果想将回退的版本推送到远程服务器，再用push。只回退本地不用此操作:

   ```
   git push -f origin master
   ```



## 使用git stash命令进行代码暂存

​	当我们开发过程中，经常会遇到开发进行了一半，需要切换到另外一个分支的情况。stash命令可以将你现在所有变动进行暂存，后续切换回来时，从暂存中恢复就可以了。stash真的是很好用的工具。

```
//暂存
git stash

//查看所有的暂存，暂存信息中包含分支名、stash版本号(形如stash@{0})
git stash list

//恢复
git stash apply  			     //恢复到最近一次stash版本
git stash pop					//恢复到最近一次stash版本，并将栈中stash版本删掉
git stash apply stash@{0} 		 //恢复到stash@{0}版本
git stash pop stash@{0}			 //恢复到stash@{0}版本，并将栈中stash@{0}版本删掉

//删除
git stash drop stash@{0}		//将栈中stash@{0}版本删除
git stash clear					//清楚栈中所有stash版本
```

为了保持git栈的清洁，推荐大家使用git stash pop相关命令

## 分支命令

```
#查看远程分支
git branch -a

#查看本地分支
git branch / git br

#创建本地分支
git branch test

#本地分支推送到远程
git push origin test

#切换分支
git checkout test

#删除本地分支
git br -d test

#删除远程分支
git branch -r -d origin/test
git push origin :test
```





## 常用命令

[Git常用命令](https://git-scm.com/docs)