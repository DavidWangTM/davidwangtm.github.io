---
layout:     post
title:      "安装fedora20后的一些操作"
subtitle:   "安装Fedora 20后的必备软件包和系统操作"
date:       2014-10-10 13:00:00
author:     "Wenzhiquan"
header-img: "img/post-bg-ios9-web.jpg"
catalog: true
tags:
    - Fedora
---

> “Fedora 20 自带的软件并不够强大，而且需要关闭防火墙和SEXLinux。”

### 1. 安装fedora20后的初始化配

##### 1.1. 防火墙

如果你的电脑处于局域网内，默认开启的防火墙会导致局域网内的其他电脑无法与你的电脑进行连接，所以，停止它！

```
sudo systemctl stop firewalld.service
sudo systemctl disable firewalld.service
```

##### 1.2. SELinux

停止[SELinux](http://baike.baidu.com/view/487687.htm?fr=aladdin"百度百科")，如果你不需要它。

```
sudo vi /etc/sysconfig/selinux
```

```
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#enforcing - SELinux security policy is enforced.
#permissive - SELinux prints warnings instead of enforcing.
#disabled - SELinux is fully disabled.
SELINUX= disabled	# change
# SELINUXTYPE= type of policy in use. Possible values are:
#targeted - Only targeted network daemons are protected.
#strict - Full SELinux protection.
SELINUXTYPE=targeted
```


### 2. 安装rpmfusion源

Fedora20的源：

```
sudo yum localinstall --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-20.noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-20.noarch.rpm
```


### 3. 安装软件包

```
sudo yum -y install yum-fastestmirror vim gnome-tweak-tool cmake gcc p7zip
```

### 4. 升级系统：

```
sudo yum -y update
```

### 5. 安装 flash plugin

##### 32位系统

```
wget http://linuxdownload.adobe.com/adobe-release/adobe-release-i386-1.0-1.noarch.rpm
sudo rpm -ivh adobe-release-i386-1.0-1.noarch.rpm
sudo yum -y install flash-plugin
```

##### 64位系统

```
wget http://linuxdownload.adobe.com/adobe-release/adobe-release-x86_64-1.0-1.noarch.rpm
sudo rpm -ivh adobe-release-x86_64-1.0-1.noarch.rpm
sudo yum -y install flash-plugin
```
