---
title: 华硕TUF-AX3000刷梅林固件
copyright: true
date: 2020-10-27 21:07:23
tags: 路由器
categories: 路由器
keywords: 华硕TUF-AX3000
description: 华硕TUF-AX3000刷梅林固件
---

# 背景

​	最近购入了新路由器，华硕TUF-AX3000，准备开始折腾。（以前用的是华为AX3 Pro，性价比也很高，算是300元内性价比之王了吧，颜值在线，wifi6标准，还能NFC连接WiFi。唯一不能满足我的就是，不能安装插件科学上网。）

# 选择

目标价位是800左右，然后有几个备选都还不错，网件R8000P、华硕RT-AC86U、华硕TUF-AX3000。奈何其余两款不支持WiFi6，购入这个路由器短期内肯定不会更换了，所以一步到位，直接上WiFi6，最终选择了华硕TUF-AX3000。

如何挑路由器就只能在网上找资料了，比较权威的还是[acwifi](https://www.acwifi.net/)。

# 购入

对比了狗东、拼夕夕，还是拼夕夕香啊，最后在拼夕夕下单。

拼夕夕有好多拆封帮忙刷入梅林固件的，我担心是返修的机器，直接买了全新版本。600多软妹币。

家里的台式网卡坏了好久，拖着一直没弄，这次顺便入了个Intel-AX3000T无线网卡，同样支持wifi6，终于重新焕发青春。这下子终于可以云顶下棋啦🥳

# 刷固件

## 找固件

机器到手后就开始了折腾，不折腾也不会买华硕了。

找固件常用的论坛有[恩山](https://www.right.com.cn/forum/)、[koolshare](https://koolshare.cn/portal.php)。

几经周折，找了好几个版本都比较旧，最终在koolshare上找到[这篇帖子](https://koolshare.cn/thread-179968-1-1.html)，是基于最新官方固件（384_9923）修改的。一起就绪，开整。

## 刷机

刷机比较简单，连上wifi后，访问router.asus.com，登入后在系统设置里，上传固件，等待刷新，升级完重启就行了。

重新登录，看到里面有了软件中心。有一些内置的插件可以直接安装，不过没有我们想要的科学上网插件。好吧，继续找。

# 安装插件

## 找插件

又是几经周折，终于找到了[ss插件](https://github.com/hq450/fancyss_history_package/tree/master/fancyss_hnd)，我这里下载的是1.8.8版本。

记住下面这些参数，找插件的时候用得到。

CPU：BCM6750，平台：axhnd.675x，架构：ARMV7，内核：4.1.52。

## 安装插件

1. 下载完成后，登录路由器管理页面，点开软件中心，然后离线安装，上传安装包。

2. 安装过程中会提示检测到非法关键词，安装失败。

   这时候需要使用ssh远程登录到路由器上（路由器ip在管理页面上有），输入管理员账号、密码，然后执行下面的命令关闭校验。（如果登录失败，提示`WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!`，到.ssh目录下删掉known_hosts文件）

   ```shell
   sed -i 's/\tdetect_package/\t# detect_package/g' /koolshare/scripts/ks_tar_install.sh
   ```

3. 重新上传安装包，然后安装，这时候提示安装成功。
4. 刷新页面，可以看到软件中心下有科学上网的插件了，点进去，配置v2ray相关的配置或者ssr的配置，然后打开开关。

发现并没有开启科学上网！！！

原因是路由器本身的内存太小，不足以支撑v2ray插件运行。

买一个好点的U盘，插到路由器上，安装虚拟内存的插件，创建1G的虚拟内存并挂载，然后就能科学上网啦！

## 路由器相关的一些命令

清空jffs，改命令会将软件中心下所有的插件删掉。

```bash
kill -9 $(pidof skipd)
cd /jffs
rm -r .[a-zA-Z_]* *
reboot
```

删除软件中心

```bash
kill -9 $(pidof skipd)
rm -rf /jffs/.asusrouter /jffs/.koolshare /jffs/db /jffs/ksdb /jffs/config/* /jffs/etc/profile
reboot
```

# one more thing

v2ray运行的时候会占用系统资源，因为路由器天生的局限性，配置不会太高，所以最近又被软路由种草了...兴冲冲的跟女票说这个事儿，又被无情的diss了...

不过一般情况路由器就够用了。除非你家宽带和机场带宽都500M以上，不然看不出来差距。

