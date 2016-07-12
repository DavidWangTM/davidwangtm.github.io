---
layout:     post
title:      "阿里云端口配置"
subtitle:   "阿里云将80端口请求转发到其他端口"
date:       2016-07-11 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-rwd.jpg"
catalog: true
tags:
    - tomcat
--- 

### 阿里云将80端口请求转发到其他端口

#### 背景
租了一台阿里云ECS，想搭建一个java Web 程序，环境都搭建好后，将Tomcat端口改为80并执行

```
./startup.sh
```

程序报错端口号冲突。
估计是80端口被占用了，但是想绑定域名，还是需要将80端口映射到tomcat才行。

于是检查占用80端口的程序

![IMG](/img/in-post/aliyun_80.png)

是一个叫`AliYunDun`的程序将80端口占用了。

#### 解决方案
由于80端口受到各种保护措施，所以一般程序是无法获取80端口的使用权的，要想实现不输入端口号直接访问程序，需要将80端口的请求转发到Tomcat设定的端口上去，也就是默认的8080端口。

#### 首先查看服务器网卡及ip设置：

![IMG](/img/in-post/aliyun_ip.png)

很明显eth1为外网网卡。

#### 设置端口号转发规则：

```
iptables -A PREROUTING -t nat -i eth1 -p tcp --dport 80 -j REDIRECT --to-port 8080
```


