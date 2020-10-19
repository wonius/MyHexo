---
title: GPU安装Docker环境
copyright: true
date: 2019-06-27 20:41:56
tags: Docker
categories: Docker
keywords:
- GPU
- Nvidia-Docker
description: GPU安装Docker、Nvidia-Docker环境
---

# nvidia驱动、cuda、cudnn、TensorFlow版本对应关系

**cuda、cudnn、docker、nvidia-docker版本一定看是否兼容。不同版本有兼容问题。**

nvidia驱动、cuda对应关系

<https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html>

cuda、cudnn对应关系

<https://developer.nvidia.com/rdp/cudnn-archive>

TensorFlow、cuda、cudnn、python对应关系

<https://tensorflow.google.cn/install/source#linux>

## 升级驱动（非必须）

如果已经安装低版本的驱动，需要升级，需要将所有使用nvidia的程序暂停（一般只有docker），再卸载。

```
# 先暂停docker
service docker stop

# 查看有哪些nvidia模块，依次卸载
lsmod | grep nvidia
rmmod nvidia-*
rmmod nvidia

# 安装nvidia-docker时，安装了一些与nvidia相关的包，一起卸载
rpm -qa | grep nvidia
rpm -e --nodeps *-nvidia-*

# 然后安装nvidia驱动、nvidia-docker
# 查看驱动版本
nvidia-smi
```



# 安装Python3

```shell
mkdir -p /usr/local/python3
tar -zxvf Python-3.6.6.tgz
cd Python-3.6.6
./configure --prefix=/usr/local/python3
make && make install
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
vim ~/.bash_profile
# 加入
export PATH=$PATH:$HOME/bin:/usr/local/python3/bin
# 验证
python3 -V
```

# 安装pip3

```shell
tar -zxvf pip-9.0.1.tgz
cd pip-9.0.1
python3 setup.py install
ln -s /usr/local/bin/pip /usr/bin/pip3
pip3 -V
```

# 安装cuda

```shell
# rpm安装
wget http://mirrors.*******/cuda-repo-rhel7-9-0-local-9.0.176-1.x86_64.rpm
rpm -ivh cuda-repo-*.rpm
yum clean expire-cache
yum install -y cuda


# 设置环境变量
vim ~/.bashrc
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH
export CUDA_HOME=/usr/local/cuda
export PATH=/usr/local/cuda/bin:$PATH
source ~/.bashrc

# 验证是否成功：
cat /usr/local/cuda/version.txt 或 nvcc --version
```

# 安装cudnn

直接将cudnn中相关文件，拷贝到cuda对应目录即可

```shell
tar -xvf cudnn-8.0-linux-x64-v6.0.tgz
# 进入cuda
cd /usr/local/cuda
# 拷贝cudnn相关文件
cp /xxx/cuda/include/cudnn.h include/
cp /xxx/cuda/lib64/libcudnn* lib64/

chmod a+r include/cudnn.h
chmod a+r lib64/libcudnn*

# 设置环境变量
vim ~/.bashrc
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH
export CUDA_HOME=/usr/local/cuda
export PATH=/usr/local/cuda/bin:$PATH
source ~/.bashrc

# 验证
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
# 查看so版本对不对
ldconfig -v | grep cudnn /
```

# 升级docker到18

```shell
# 卸载原来docker
# rpm安装，直接remove
yum remove docker
# 如果是二进制文件拷贝，需要手动删除
cd /usr/local/bin
rm -rf docker*

# 安装docker18
rpm -ivh containerd.io-1.2.2-3.el7.x86_64.rpm
rpm -ivh container-selinux-2.74-1.el7.noarch.rpm
rpm -ivh docker-ce-cli-18.09.5-3.el7.x86_64.rpm
rpm -ivh docker-ce-18.09.5-3.el7.x86_64.rpm
# 默认是安装在/usr/bin目录下，根据个人需要看是否需要拷贝到/usr/local/bin
cd /usr/bin
cp docker* /usr/local/bin

systemctl status docker.service
docker --version
```

# 安装nvidia-docker

```shell
rpm -ivh libnvidia-container1-1.0.2-1.x86_64.rpm
rpm -ivh libnvidia-container-tools-1.0.2-1.x86_64.rpm
rpm -ivh nvidia-container-runtime-hook-1.4.0-2.x86_64.rpm
rpm -ivh nvidia-container-runtime-2.0.0-3.docker18.09.5.x86_64.rpm
rpm -ivh nvidia-docker2-2.0.3-3.docker18.09.5.ce.noarch.rpm

# 查看版本
nvidia-docker --version

# 另外修改daemon.json，使用docker run的时候默认使用nvidia-docker
vim /etc/docker/daemon.json
{
    "default-runtime": "nvidia",
    "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
        }
    }
}

# 安装完成之后，重启docker、nvidia-docker
systemctl daemon-reload
systemctl enable docker
systemctl restart docker
```
