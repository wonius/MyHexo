---
title: Linux常用命令
date: 2018-01-16 21:58:57
tags: Linux
categories: Linux
keywords: 
- cat
- head
- tail
- grep
- find
- tar
description: Linux常用命令
---

## cat/grep/sed-文件内容查看

1. cat、head/tail组合

   ```shell
   //查看前1000行
   head -n 1000 filename
   //查看后1000行
   tail -n 1000 filename
   //查看1000之后
   tail -n +1000 filename
   //实时查看
   tail -f filename

   //查看最后1000行数据
   cat filename | tail -n 1000
   //查看1000到3000行的数据
   cat file | head -n 3000 | tail -n +1000
   ```

2. grep

   ```shell
   //匹配文件中所有foo关键字上下1000行
   grep -C 1000 foo filename

   //匹配文件中所有foo关键字及前1000行
   grep -B 1000 foo filename

   //匹配文件中所有foo关键字及后1000行
   grep -A 1000 foo filename
   ```

3. sed

   ```Shell
   //显示第1000到3000行的数据
   sed -n '1000,3000p' filename
   ```

## find-文件搜索

```shell
find / -name filename
```

## ps-查找进程

```shell
ps -ef | grep XXX

kill -9 pid
```

## scp-跨服务器拷贝

```shell
scp file user@localhost:dir
```

## tar-压缩、解压

1. 压缩

```shell
#只打包
tar -cvf file.tar file
#使用gzip压缩，再打包
tar -czf file.tar.gz file
#使用bzip2压缩，再打包
tar -cjf file.tar.bz2 file
#使用compress压缩，再打包
tar -cZf file.tar.Z file

#rar压缩,需先安装rar服务
rar a file.rar file
#zip压缩，需先安装zip服务
zip file.zip file
```

2. 解压

```shell
#解压常规包
tar -xvf file.tar
#解压gzip包
tar -xzvf file.tar.gz
#解压bzip2包
tar -xjvf file.tar.bz2
#解压compress包
tar -xZvf file.tar.Z

#解压rar
unrar e file.rar
#解压zip
unzip file.zip
```

3. 参数解析

```shell
#必须参数，且只能选一个
-c:	建立压缩文档
-x:	解压
-t:	查看文件
-r:	压缩文件末尾追加文件
-u:	更新原压缩文件中的文件

#可选参数，根据需要添加
-z: gzip
-j:	bz2
-Z:	compress
-v:	显示所有过程
-O:	将文件解开到标准输出

#必选参数
-f: 在所有参数最后，后面接文档名
```

## 创建软连接

```shell
#源文件指要被链接的文件，目标文件指新建的软连接
ln -s 源文件 目标文件
```

