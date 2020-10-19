---
title: RDBMS那点事儿之Undo-log、Redo-log和binlog
copyright: true
date: 2020-03-11 16:31:04
tags: DB
categories: DB
keywords: 
- 事务
- binlog
- undolog
- redolog
description: 关系型数据库中Undo log、Redo log、binlog相关的一些知识点
---

# 概述

## undo log

当数据变更时，记录数据原始的状态。分两级策略，undo buffer和undo log。

undo buffer即在内存中，这种状态的数据是不稳定的，一般会立即持久化到磁盘中的file文件，即undo log。不同数据库undo log存放位置不同。对于Mysql来说，Mysql5.7后的版本最好单独创建undo表空间，避免一次更新大批量数据生成大量undo log却无法存储。

在事务开始之前生成。

## redo log

当数据变更时，记录数据要变更的状态。也分两级，redo buffer和redo log。

同undo log，redo buffer也存储在内存中，redo log持久化在磁盘上的file文件。

数据库收到更新请求，先查询原数据，写到内存（记为data buffer），然后生成redo buffer，持久化为redo log。之后更新data buffer，在持久化到data file。

在事务开始之后生成。如果事务比较大，在中间执行过程中都会生成，并不是事务提交时一次性生成。

同时会记录事务的状态，包括事务编号（事务开始时生成）、开始状态、结束状态。判断事务是否成功即根据该文件中事务的状态。

不同类型数据库存储格式不一样，有的undo log、redo log分开记录，格式如<事务ID，变更项，变更前内容>和<事务ID，变更项，变更后内容>。有些记录在同一个文件，格式如<事务ID，变更项，变更前内容，变更后内容>。

## binlog

用于mysql节点之间同步用的，基本可以做到实时同步。目前也有很多基于binlog实现的跨库同步，比如canal。

在事务提交时一次性写入。所以开启该功能时会一定程度上影响效率。

## 执行过程

再完整的梳理一下完整的执行过程，当使用事务时：

```shell
生成事务ID -> 查询原值写到data buffer -> undo log（包括buffer到file）-> 事务开始 -> redo log（包括buffer到file）-> 执行操作（update或其他，操作过程中实时写redo log）-> 事务完成 -> binlog写入
```

中间任何环节出现问题，比如重启，以redo log中的事务状态为依据，判断是否需要回滚。

以上说的是单个事务的流程。但是我们知道最头疼的永远是并发的场景。

聊到这里了，顺便聊聊并发。

# 并发情况下产生的问题

假设有两个事务T1、T2在同时进行。

## 脏读

当T1执行过程中，对某条数据做了update，T2读取了该数据，但是最终T1回滚，T2再读数据，发现两次数据不一致。

关键点在于T1没有执行成功。

隔离级别为`Read Uncommitted`级别会出现该问题。

将隔离级别设置为`Read Committed`可解决该问题。Sql Server、Oracle模式是该级别。

## 不可重复读

T1执行过程中，先读取了某条数据，然后T2对于该数据做了update，T1再读，发现两次数据不一致。

关键点在于，在T1执行过程的两次读之间，T2已经做完变更了。

将隔离级别设置为`Repeatable Read`可解决该问题。Mysql默认是该级别。

## 幻读

T1执行过程中，查询符合某些条件的一批数据，然后T2做了insert操作，T1在重新执行查询语句，发现多了一条数据。

幻读与不可重复读有些类似，主要区别点在于所做的操作。不可重复读仅对记录做update，所以只要锁记录就可以解决。幻读是执行的insert操作，需要对表进行控制。

将隔离级别设置为`Serializable`可解决该问题。该级别下事务不能并发执行，只能顺序执行，所以性能也是最差的。

