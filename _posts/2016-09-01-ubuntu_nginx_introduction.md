---
layout:     post
title:      "Ubuntu 操作篇二"
subtitle:   "Ubuntu 安装Nginx"
date:       2016-09-01 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Ubuntu
    - Nginx
--- 

### 从Ubuntu packages安装

```
sudo apt-get install nginx
```

但是此时安装的nginx版本老旧，并且无法安装第三方扩展。但是大多数linux发行版支持更新最新的安装包。

1.首先获取nginx认证钥匙以便包管理器认证nginx包

```
wget http://nginx.org/keys/nginx_signing.key
```

2.用apt-key命令添加下载的钥匙

```
sudo apt-key add nginx_signing.key
```

3.获取发行版的codename

```
lsb_release -a
```

```
No LSB modules are available.

Distributor ID:	Ubuntu

Description:	Ubuntu 16.04 LTS

Release:	16.04

Codename:	trusty
```

4.打开/etc/apt/sources.list,添加命令：

```
deb http://nginx.org/packages/ubuntu/ codename nginx

deb-src http://nginx.org/packages/ubuntu/ codename nginx
```
codename是第三步得到的。

5.更新apt-get，并安装nginx

```
sudo apt-get update

sudo apt-get install nginx
```

安装完成之后用nginx -V查看一下吧。





