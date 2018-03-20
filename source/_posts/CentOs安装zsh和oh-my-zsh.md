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

*添加插件、换themes修改文件*



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

### 主题设置

```
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

```
alias | grep git
```

#### zsh-autosuggestions（命令补全）

```
//安装插件
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

//添加配置
vim ~/.zshrc
//找到plugins，将zsh-autosuggestions添加到括号里
```

#### zsh-syntax-highlighting（命令高亮）

```
//安装插件
git clonehttps://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting

//添加配置
vim ~/.zshrc
//找到plugins，将zsh-syntax-highlighting添加到括号里
```

#### autojump（目录快速跳转）

```
//安装autojump
brew install autojump

//配置.zshrc。下面这段，在安装完成后会有提示，直接拷贝
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
```

其他系统安装方法可以到官方上去看。另外注意，只有进过的目录，才会直接进入

https://github.com/wting/autojump

#### 更多插件

https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins



<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="知识共享许可协议" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />本作品由<a xmlns:cc="http://creativecommons.org/ns#" href="http://wonius.top/" property="cc:attributionName" rel="cc:attributionURL">Gavin</a>采用<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a>进行许可。