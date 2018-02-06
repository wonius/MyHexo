---
title: CentOS安装zsh和oh-my-zsh
date: 2017-12-18 22:05:54
tags: 
- shell
- zsh
categories: Linux
keywords: 
- CentOs
- zsh
- oh-my-zsh
description: CentOs安装zsh和oh-my-zsh
---

### 查看系统当前的shell
```
echo $SHELL
```
返回结果如下：

```
/bin/bash
```

*PS.默认的shell一般都是bash*

### 查看bin下是否有zsh包

```
cat /etc/shells
```

返回结果如下：

```
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/tcsh
/bin/csh
```

*PS：默认没有安装zsh*

### 安装zsh包

```
yum -y install zsh
```

安装完成后查看shell列表：

```
cat /etc/shells
```

返回结果如下：

```
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/tcsh
/bin/csh
/bin/zsh
```

### 切换shell至zsh

```
chsh -s /bin/zsh
```

chsh用法请自行查找，返回结果如下：

```
Shell changed.
```

此时查看shell，已经更换为zsh：

```
echo $SHELL
```

返回结果如下：

```
/bin/bash
```

需要重启才能生效：

```
sudo reboot
```

重启后查看shell：

```
echo $SHELL
```

返回结果：

```
/bin/zsh
```

### 安装git

```
yum -y install git
```

### 安装oh-my-zsh

```
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

出现如下界面显示安装成功：

```
         __                                     __   
  ____  / /_     ____ ___  __  __   ____  _____/ /_  
 / __ \/ __ \   / __ `__ \/ / / /  /_  / / ___/ __ \ 
/ /_/ / / / /  / / / / / / /_/ /    / /_(__  ) / / / 
\____/_/ /_/  /_/ /_/ /_/\__, /    /___/____/_/ /_/  
                        /____/                       ....is now installed!
Please look over the ~/.zshrc file to select plugins, themes, and options.

p.s. Follow us at https://twitter.com/ohmyzsh.

p.p.s. Get stickers and t-shirts at http://shop.planetargon.com.
```

*添加插件、换themes修改~/.zshrc文件*



如果执行命令时提示==warning: cannot set LC_CTYPE locale==，修改profile文件：

```
vim /etc/profile
```

在文件末尾处添加：

```
export LC_ALL=en_US.UTF-8
export LC_CTYPE=en_US.UTF-8
```

修改后更新：

```
source /etc/profile
```

