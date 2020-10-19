---
title: Redis批量改名key
copyright: true
date: 2018-04-13 11:04:12
tags: 
- Redis
- Shell
categories: DB
keywords: 
- Redis
- rename
- shell
description: Redis中的key批量改名shell脚本
---

## 背景

​	最近接到一个任务，需要将redis中的key进行rename。之前的结构为XXX-XXX-XXX-\*，替换为XXX:XXX:XXX:*。

## 思路&方案

​	第一反应想到是用正则，匹配出对应的key，然后利用redis中也有rename命令，支持重命名。尽量都在redis中处理。

​	后来调研了一下，redis中没有类似于find的命令😩，唯一可用的是keys命令，所以调整方案，使用keys+pattern匹配出所有对应的key，流向shell，shell中对key循环，转换key名。

​	其中还有一个坑，keys虽然支持正则，但是貌似只支持\*，?、\+、^都不支持。库中新结构、老结构的数据都有，所以只能枚举了

## 脚本

```
//创建shell脚本
vim rename.sh

//拷贝如下内容
#!/bin/bash
##redis主机IP
host=127.0.0.1
##redis端口
port=6379
##密码
passwd=123456
##pattern模式
# patterns=("[a-zA-Z]*-[a-zA-Z0-9]*" "[a-zA-Z]-[a-zA-Z0-9]*-[a-zA-Z0-9]*" "[a-zA-Z]-[a-zA-Z0-9]*-[a-zA-Z0-9]*-*")
patterns=("Answer-*-*-*" "FaqInfo-*-*" "ModelInfo-*-*" "RelatedFaq-*-*-*" "SensitiveWord-*" "Term-*-*-*")

##备份redis
redis-cli -h $host -p $port -a $passwd save
echo '备份至=>'
redis-cli -h $host -p $port -a $passwd config get dir

for p in ${patterns[@]}
    do
    ##循环获取key并删除
    re=$(redis-cli -h $host -p $port -a $passwd --raw keys $p)
    echo $re
    ##转成数组
    arr=($re)
    ##打印数组长度
    echo 'arr.len:'${#arr[@]}
    ##循环数组
    for key in ${arr[@]}
        do
            # echo $key
            # echo "key:"$key
            newKey=$(echo ${key/\-/\:})
            newKey=$(echo ${newKey/\-/\:})
            newKey=$(echo ${newKey/\-/\:})
            echo $newKey
            redis-cli -h $host -p $port -a $passwd rename $key $newKey
    done
done

echo 'done'

//授执行权限
chmod 777 rename.sh

//执行
sh rename.sh XXXX XX XX
```

## 恢复备份

​	脚本中会先将当前库中内容进行备份，备份到脚本运行目录中，文件名为dump.rdb。

恢复备份操作：

1. 停止Redis服务：`redis-cli shutdown`；
2. 将dump.rdb文件拷贝到redis安装目录下的bin目录（我的是/usr/local/Cellar/redis/3.2.9/bin）；
3. 重启redis服务 `redis-server &`;