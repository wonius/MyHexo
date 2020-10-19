---
title: Oracle编码问题
copyright: true
date: 2020-05-11 15:45:01
tags: Oracle
categories: DB
keywords: Oracle
description: Oracle编码问题
---

最近被Oracle的编码问题折腾的头都大了，在这里总结一下。

在Oracle搭建，以及初始化脚本过程中，共有4处涉及到编码问题。

关于编码问题，实际上讲的是如何设置编码，保证中文不乱码。如果是仅是英文字符，不会有乱码问题的。

有一点需要区分，命令执行环境是在shell，还是在sqlplus。

# 操作系统字符集

可通过locale和env | grep LANG命令查看。

该编码对于Oracle无影响，因为Oracle有自己的字符集，优先使用自己的字符集。

就算两台主机的字符集不同，只要Oracle的字符集相同，也可以正常使用。

只有在当前主机做调整时，才会依赖主机字符集。比如在shell中执行数据库的更新查改操作，其中涉及到主机编码与oracle编码互转的过程。如果两边编码不匹配，也会乱码。

需要强调的一点是，sqlplus如果本身没有设置字符集，会默认使用操作系统的字符集。所以在使用sqlplus执行脚本的时候，要注意NLS_LANG设置为AMERICAN_AMERICA.ZHS16GBK，保证中文不乱码。

# Oracle字符集

在建库(create database)时，可以设置该库的字符集，一般选择AL32UTF8。

当登入sqlplus之后，执行sql语句做插入或者更新操作时，存入的数据以该字符为准。

# 脚本字符集

这点指的是sql脚本文件中的编码字符集。

一般来讲我们常用的只有两种，UTF8或者GB2312。

当脚本中全英文时，任何编码都可以。

当脚本中涉及到中文字符时，需要保存为GB2312格式。

# 文件格式

常用编辑器，比如UE、Notepad++，在下方状态栏都会显示文件格式，总共有三种格式，UNIX、Dos\Windows、MAC。

这个编码标明该文件是在什么操作系统下编辑生成的。

我们的脚本直接在sqlplus中执行，已测试过使用UNIX、Dos\Windows都可以正常执行。

如果是在shell中执行，需要改成UNIX。之前我在win7上编写过一个Dockerfile，上传到主机执行报错，改为UNIX才正常执行。

在Linux主机上，可以再vim中查看、修改文件格式。输入`:set ff`查看，输入`:set ff=unix`修改。

# ssh工具编码

这点应该不用多说了，相信大家都有经验。不管是shell，或是sqlplus中执行命令，如果最终要展示的结果中包含中文，就算主机编码正确，如果工具编码不对，也会展示乱码。

同样，一般设置为UTF8。

为了避免工具对数据库中数据编码的影响，我一般的操作都是，将sql脚本上传到Oracle主机上，然后登陆sqlplus，使用`@xxx/xxx/xxx.sql`的方式，直接加载脚本文件执行。