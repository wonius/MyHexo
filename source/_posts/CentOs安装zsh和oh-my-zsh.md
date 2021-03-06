---
title: CentOS安装zsh和oh-my-zsh
date: 2017-12-18 22:05:54
tags: 
- Shell
- zsh
categories: Shell
keywords: 
- CentOS
- zsh
- oh-my-zsh
description: CentOS安装zsh和oh-my-zsh
copyright: true
---

### 查看系统当前的shell
```bash
echo $SHELL
```
返回结果如下：

```bash
/bin/bash
```

*PS.默认的shell一般都是bash*

### 查看bin下是否有zsh包

```bash
cat /etc/shells
```

返回结果如下：

```bash
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/tcsh
/bin/csh
```

*PS：默认没有安装zsh*

### 安装zsh包

```bash
yum -y install zsh
```

安装完成后查看shell列表：

```bash
cat /etc/shells
```

返回结果如下：

```bash
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/tcsh
/bin/csh
/bin/zsh
```

### 切换shell至zsh

```bash
chsh -s /bin/zsh
```

chsh用法请自行查找，返回结果如下：

```bash
Shell changed.
```

此时查看shell，已经更换为zsh：

```bash
echo $SHELL
```

返回结果如下：

```bash
/bin/bash
```

需要重启才能生效：

```bash
sudo reboot
```

重启后查看shell：

```bash
echo $SHELL
```

返回结果：

```bash
/bin/zsh
```

### 安装git

```bash
yum -y install git
```

### 安装oh-my-zsh

```bash
wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | sh
```

出现如下界面显示安装成功：

```bash
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

*添加插件、换themes修改文件*



如果执行命令时提示==warning: cannot set LC_CTYPE locale==，修改profile文件：

```bash
vim /etc/profile
```

在文件末尾处添加：

```bash
export LC_ALL=en_US.UTF-8
export LC_CTYPE=en_US.UTF-8
```

修改后更新：

```bash
source /etc/profile
```

### 主题设置

```bash
//查看系统自带的主题
ls ~/.oh-my-zsh/themes

//找到ZSH_THEME，将后面的值改为自己喜欢的主题
vim ~/.zshrc

//设置其他主题，先将主题安装到$ZSH_CUSTOM/themes目录，再设置
```

推荐几个主题：

* ys
* aussiegeek
* bira
* agnoster

更多主题：

https://github.com/robbyrussell/oh-my-zsh/wiki/themes

### 插件设置

这里列举几个常用的插件，设置方法一样。插件很多，按需索取。否则会影响shell加载速度

#### git

默认自带git插件，可以用下面命令查看有哪些快捷指令

```bash
alias | grep git
```

#### zsh-autosuggestions（命令补全）

```bash
//安装插件
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

//添加配置
vim ~/.zshrc
//找到plugins，将zsh-autosuggestions添加到括号里
```

#### zsh-syntax-highlighting（命令高亮）

```bash
//安装插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting

//添加配置
vim ~/.zshrc
//找到plugins，将zsh-syntax-highlighting添加到括号里
```

#### autojump（目录快速跳转）

```bash
//安装autojump
brew install autojump

//配置.zshrc。下面这段，在安装完成后会有提示，直接拷贝
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
```

其他系统安装方法可以到官方上去看。另外注意，只有进过的目录，才会直接进入

https://github.com/wting/autojump

#### 更多插件

https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins
