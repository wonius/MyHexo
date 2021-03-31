---
title: Solr演练
copyright: true
date: 2018-09-09 13:20:58
tags: Solr
categories: 中间件
keywords: Solr
description: Solr实战演练Part 1
---

# 下载

1. 访问[官网](https://www.apache.org/dyn/closer.cgi)进行下载；
2. 解压即可；

# 启动

1. 进到solr目录，执行`bin/solr start -e cloud`，多节点运行；
2. 分别提示创建几个节点、第一个节点端口、第二个节点端口，按enter默认就可以；
3. 输入集合名称，这里我们可以设置为techproducts；
4. 然后提示创建几个分片、创建几个副本，这些默认2就可以；
5. 然后选择使用哪组配置文件，总共有_default、sample_techproducts_configs两个选项，我们选后者；
6. 创建完成，可以通过`localhost:8983`进入solr管理页面，在cloud中查看集合的结构；

**如果第一步时报错：**

使用普通用户：

`ERROR: Logs directory /etc/solr-7.4.0/example/cloud/node1/solr/../logs is not writable. Exiting`

或者

使用root用户：

`ERROR: Failed to start Solr using command: "bin/solr" start -cloud -p 8983 -s "example/cloud/node1/solr" Exception : org.apache.commons.exec.ExecuteException: Process exited with an error: 1 (Exit value: 1)`

是因为用户访问权限不对，修改下权限就可以。我这里直接开了所有权限

```bash
sudo chmod -R 777 /etc/solr-7.4.0
```

# 索引数据

Solr目录中的example提供了基础数据，我们可以把这些基础数据加载到刚才创建的techproducts集合，并建立索引：

`bin/post -c techproducts example/exampledocs/*`

# 搜索

在Solr Admin中可以自己定义查询条件进行查询。

1. 先选中创建的techproducts集合，然后选择Query；
2. 在q中输入自己的查询条件，默认\*:\*查询所有。比如`id:123`，就是查询id为123的内容；
3. 多条件查询，中间用`+`连接，如：`+(features:"CAS latency")+(id:"VDBDB1A16")`；相反，如果希望结果中过滤掉某个属性的内容，用`-`连接，如：``+(features:"CAS latency")-(id:"VDBDB1A16")``
4. fl对查询结果进行过滤，仅显示fl中的属性。多个属性`,`间隔；

# Solr其他命令

### 删除集合

```bash
bin/solr delete -c techproducts
```

### 创建新集合

```bash
bin/solr create -c <yourCollection> -s 2 -rf 2
```

### 停止

```bash
bin/solr stop -all
```

### 重新启动

```bash
bin/solr start -c -p 8983 -s example/cloud/node1/solr
bin/solr start -c -p 7574 -s example/cloud/node2/solr -z localhost:9983
```

