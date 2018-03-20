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

#创建本地分支test1，并跟踪远程分支ci
git checkout -b test1 origin/ci
#推送到远程仓库
git push origin test1

#切换分支
git checkout test

#删除本地分支
git br -d test

#删除远程分支
git branch -r -d origin/test
git push origin :test 
```

## fatal: refusing to merge unrelated histories

通常是因为本地仓库的历史记录，与远程仓库的历史记录不同，git认为是两个完全无关的项目，无法pull

解决办法：

```
git pull origin master ----allow-unrelated-histories
```

## 代码提交到不同仓库

1. 手动分别推送（如果需要两个库代码不同）

```

//添加仓库，并设置别名
git remote add github https://*****.git
git remote add gitlab https://*****.git

//推送
git push github master:master
//如果是新建的仓库，需要加-f参数
git push -f github master:master
```

2. 一键push所有库（两个库代码完全一样）

```
//将新仓库URL绑定到origin上
git remote set-url --add origin https://*****.git
//push
git push

//查看git中配置的url
git config -e
```





## 常用命令

[Git常用命令](https://git-scm.com/docs)





<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品由<a xmlns:cc="http://creativecommons.org/ns#" href="http://wonius.top/" property="cc:attributionName" rel="cc:attributionURL">Gavin</a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。