---
title: configMap的应用
copyright: true
date: 2020-10-21 16:12:36
tags: 
- configMap
- K8S
categories: K8S
keywords: 
- configMap
- K8S
description: configMap的简单应用
---

# 背景

​	我们系统的微服务是通过K8S容器化部署的，由于某些原因，没有对接配置中心。所以为了将配置与应用分离，便于后续修改配置，决定使用configMap+deployment的方式，将配置文件外挂到应用上。

# 步骤

## 思路

​	整体思路是，创建application.yml配置文件，应用打包时将该文件打到包内。然后将每个环境的

## 修改应用

修改应用，指定配置文件为application.yml

## 创建configMap

```bash
kubectl create cm xxx-config --from-file=ci=xxxx.yml --from-file=stg=xxx.yml --from-file=prod=xxx.yml
```

## 修改deployment

```yaml
# mountPath
- name: cm
  mountPath: /xx/xx/application.yml
  subbPath: application.yml
  
# volumes
- name: cm
  configMap: 
    name: xxx-config
    items:
      - key: ci
        path: application.yml
```

修改完成后部署验证

