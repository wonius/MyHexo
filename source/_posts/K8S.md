---
title: 通过K8S传参创建Job
copyright: true
date: 2019-07-25 20:03:58
tags: K8S
categories: K8S
keywords: K8S
description: 通过K8S传递参到镜像，创建Job
---

# 打包镜像

编写Dockerfile

```dockerfile
...

# 我的镜像是python项目，需要传参进去，设置环境变量PARAMS
ENTRYPOINT python main.py -p $PARAMS
```

然后打包镜像

# 编写yaml

我要跑的job是一个TensorFlow训练服务，只想跑在GPU的机子上，同时避免为了其他服务跑到GPU上

所以在GPU机子上打了标签、污点

在Job里需要选择标签、容忍

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: myJob
spec:
  template:
    metadata:
      name: myJob
    spec:
      restartPolicy: OnFailure
      # 设置容忍
      tolerations: 
        - key: "app"
          operator: "Equal"
          value: "GPU"
          effect: "NoSchedule"
      containers:
      - name: myJob
        # 设置环境变量，传值
        env:
        - name: PARAMS
          value: ""
        # 镜像地址
        image: hub.xxxxx/
        volumeMounts:
        - name: nas
          mountPath: /nas
      volumes:
      - name: nas
        hostPath:
          path: /nas
      nodeSelector:
        app: "GPU"
```

然后运行该yaml

`kubectl apply -f myJob.yaml`

