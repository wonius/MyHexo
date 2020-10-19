---
title: Oracle高可用架构
copyright: true
date: 2020-05-12 11:05:42
tags:
- Oracle
- 高可用架构
categories: DB
keywords: 
- Oracle
- 高可用架构
description: Oracle高可用架构
---

最近了解了一些Oracle方面的高可用架构，记录一下。

有时间补上架构图。

# DG架构

该架构是传统的主备架构，也是最常用的架构，满足一般系统的要求。

首先，申请两台完全同构的主机，挂载相同的NAS，并安装Oracle，修改设置。一般primary机对外提供服务，其他standby机备份。

每台Oracle主机都是独立的主机与存储，通过同步归档日志和在线日志实现数据同步。

一般DG架构中每台主机都会挂载一块NAS，Oracle数据和日志存放在NAS上。

建tablespace时指定datafile到NAS上。

当异常情况时，切换备机，切换时间一般为几分钟。

```sql
-- 常规模式，创建名为dbdata的表空间
create tablespace dbdata
  logging
  datafile '/xxx/xxx/data.dbf' size 500m
  autoextend on
  extent management local autoallocate
  segment space management auto ;
```

PS：这里讲下DG和OGG主要的区别。DG效率更高、更稳定、占用资源少，但是只支持Oracle间同步。OGG效率、稳定性、资源占用都要差一点，但是支持不同数据库间同步。

# RAC架构

DG只使用Oracle就可以完成。

RAC架构需要与Oracle的另外一种技术，ASM（Automatic Storage Management），结合使用。基本原理是，不同Oracle主机共享同一块存储，就不需要数据同步了，而ASM是Oracle开发的管理存储的工具。

该模式下需要先建立一个ASM实例，然后不同Oracle主机使用该ASM，创建表空间时指定该ASM。

RAC、ASM对于硬件的要求更高，且由于共享数据，基本可以实时切换。一般核心系统

```sql
-- ASM模式，ASM实例名为+data_asm，创建名为dbdata的表空间
create tablespace dbdata
  datafile '+data_asm' size 500m
  autoextend on;
```

# 多机房架构

不论是DG架构，还是RAC架构，都在单一机房。

当企业对于数据有更高要求时，就要考虑多机房架构了。

根据是否同城又有同城备份，异城备份，以及两地三备份。

就说两地三备份吧，总共涉及3个机房，主机房A，同城机房B，异地机房C。

在主机房A中Oracle可以选择DG或者RAC架构（对于数据要求这么高，应该得用RAC了吧）。

同城机房B和异地机房C，视要求搭建。不同库间使用DG同步，同步链路为A->B，A->C。