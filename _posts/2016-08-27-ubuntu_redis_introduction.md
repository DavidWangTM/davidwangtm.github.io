---
layout:     post
title:      "Ubuntu 操作篇"
subtitle:   "Ubuntu 安装设置redis"
date:       2016-08-26 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Ubuntu
    - Redis
--- 

## ubuntu 14.04下的redis安装配置

### 安装

```
sudo apt-get install redis-server
```

### 检查redis系统进程

```
ps -aux|grep redis
```

### redis服务器状态检查

```
sudo /etc/init.d/redis-server status
```

或

```
netstat -nlt|grep 6379
```

### 通过终端访问redis

```
redis-cli
```

### 重启redis

```
sudo /etc/init.d/redis-server restart
```

### 配置redis

默认情况下，redis的访问是不需要账号和密码的。

要增加账号和密码，可以通过修改配置文件实现。

```
sudo vim /etc/redis/redis.conf
```

取消掉注释

```
#取消注释requirepass
requirepass xxxx
```

重启redis服务器

带密码登录

```
redis-cli -a xxx
```

### 访问远程redis服务器

注：默认情况下，redis服务器仅能本地方位。远程访问需注释掉配置文件中的“bind”项。

```
redis-cli -a xxx -h 192.168.1.200
```