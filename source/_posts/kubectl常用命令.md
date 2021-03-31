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

# Pod

```bash
# 查看所有pod
kubectl get po -o wide

# 查看pod启动日志
kubectl logs xxx
# 也可以写到文件中，查看方便
kubectl logs xxx > xxx.log

# 查看pod的详细信息
kubectl describe po xxx

# 进入pod内部
kubectl exec -it xxx bash
```

# Deployment

```bash
# 编辑deployment
kubectl edit deployments xxx -o yaml
```

# Service

```bash
# 查看所有service
kubectl get svc

# 查看某个service
kubectl describe svc xxx
```

# ConfigMap

```bash
# 从文件创建configMap
# AA：configMap的名称
# BB：configMap中key的名称
# CC.yml：源文件
kubectl create cm AA --from-file=BB=CC.yml

# 将configMap导出为yaml文件
kubectl get cm AA -o yaml > BB.yaml
```

# Ingress

