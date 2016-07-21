---
layout:     post
title:      "Mac下配置redis"
subtitle:   "redis安装与运行"
date:       2016-07-20 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-rwd.jpg"
catalog: true
tags:
    - redis
    - Mac
--- 
### Mac 下安装运行 redis

`mac` 上安装 redis 首先必须保证 `mac` 已经安装 `xcode`.

#### 一.下载redis

redis下载地址：[https://code.google.com/archive/p/redis/downloads](https://code.google.com/archive/p/redis/downloads)

#### 二.配置redis

```
//依次运行
sudo make
sudo make test
sudo make isntall
```

运行完成后，将配置文件移动到/etc目录下.

#### 三.启动redis

```
/usr/local/bin/redis-server
//或者
/usr/local/bin/redis-server /etc/redis.conf
```
![IMG](/img/in-post/redis.png)

#### 四.尝试连接redis，进行数据操作

进入到`/usr/local/bin`目录，输入 `./redis-cli`即可进入编辑页面
