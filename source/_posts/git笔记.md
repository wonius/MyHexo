---
title: Git学习笔记
date: 2017-07-14 00:06:02
tags: Git
categories: 版本管理
keywords: Git
description: Git常用命令
copyright: true
---

## 本地代码初始化到远程仓库

```
//create a new repository on the command line
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/wonius/XXXXXX.git
git push -u origin master

//push an existing repository from the command line
git remote add origin https://github.com/wonius/MySpringBootDemo.git
git push -u origin master
```



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

// 删除别名
git remote remove github

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

## 修改远程仓库地址

1. 命令修改

   ```
   git remote set-url origin http://*****.git
   ```

2. 先删除再添加

   ```
   git remote rm origin
   git remote add origin http://*****.git
   ```

3. 配置文件修改

   ```
   cd ***/.git
   vim config
   # 然后修改
   ```

## rebase提交

通常我们都会拉取分支，提交代码时从自己分支往主分支上merge，当分支比较多时，分支树会显得很乱。

使用rebase，将自己分支每次重置为主分支记录，再提交，全程只有主分支的提交历史，比较清晰

```
git add .
git commit -m "*****"
git rebase master(主分支)
git rebase --continue
git push
```

## 修改commit内容

```
git commit --amend
# 修改内容
git push -f
```

## 常用命令

[Git常用命令](https://git-scm.com/docs)
