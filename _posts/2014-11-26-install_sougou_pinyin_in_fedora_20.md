---
layout:     post
title:      "fedora20 安装搜狗输入法"
subtitle:   "卸载ibus，启用fcitx"
date:       2014-11-26 14:00:00
author:     "Wenzhiquan"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - Fedora
---

> “Fedora自带的输入法对中文的支持并不够好，可以安装搜狗输入法进行替代”

### 0.环境描述

```
系统环境: Fedora 20  64位
搜狗输入法版本: sogou_pinyin_linux_1.1.0.0037_amd64
```

### 1.卸载ibus

```
sudo yum remove ibus
gsettings set org.gnome.settings-daemon.plugins.keyboard active false    #解除gnome桌面与ibus守护进程的绑定(必须有这一步)
```

### 2.安装fcitx输入法
 linux 搜狗输入法使用fcitx作为输入平台,因此要先安装fcitx输入法:

```
sudo yum install fcitx fcitx-configtool
```

检查安装结果:

```
fcitx -v
显示:fcitx version: 4.2.8.4
```

说明fcitx的当前版本为4.2.8.4,安装成功

下一步需要配置Fcitx的环境:在~/.bashrc中加入一下内容:

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

保存后重启电脑

### 3.安装搜狗拼音输入法

到[官网]( http://pinyin.sogou.com/linux/ "搜狗输入法官网")下载最新版本的搜狗拼音输入法,我下载的是`sogou_pinyin_linux_1.1.0.0037_amd64.deb`

下载完成后,切换到deb包所在的目录,执行以下命令:

```
ar vx sogou_pinyin_linux_1.1.0.0037_amd64.deb
```

此时会获取包中的文件:

```
x - debian-binary
x - control.tar.gz
x - data.tar.xz
```

然后执行以下命令,将包中的内容配置到系统安装目录当中:

```
sudo tar -Jxvf data.tar.xz  -C /
```

,在fcitx中导入搜狗库,使fcitx识别并统一管理搜狗拼音,执行以下命令:

```
sudo cp /usr/lib/x86_64-linux-gnu/fcitx/fcitx-sogoupinyin.so  /usr/lib64/fcitx/fcitx-sogoupinyin.so
```

### 4.启动搜狗拼音输入法

打开终端,启动fcitx和搜狗拼音输入法:

```
fcitx
sogou-qimpanel
```

为了避免每次启动都要自己手动输入此命令,可以将搜狗拼音输入法加入开机启动项之中
