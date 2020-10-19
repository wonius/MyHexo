---
title: kubectl常用命令
copyright: true
date: 2018-07-21 20:08:00
tags: Kubernetes
categories: K8S
keywords:
- kubernetes
- kubectl
description: kubectl常用命令
---

# 查看pod

```
kubectl get po -o wide
```

# 获取pod日志

```
//写到文件中更方便查看
kubectl logs **** > ***.log
```

# 编辑deployment

```
kubectl edit deployments *** -o yaml
```

# 进入bash

实例已经启动

```
kubectl exec -it **** bash
```

