---
layout:     post
title:      "Mac系统Docker 安装及配置"
subtitle:   "在 MAC 系统安装 Docker"
date:       2016-10-06 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-rwd.jpg"
catalog: true
tags:
    - Java
    - Docker
--- 

## Mac系统Docker,安装配置及使用

### 安装

在Mac系统上下载[Docker](https://www.docker.com/)有两种方式，一种是下载stable Docker，另一种是下载Beta版本的Docker。

#### stable Docker下载

废话就不多说了直接上下载链接：[https://download.docker.com/mac/stable/Docker.dmg](https://download.docker.com/mac/stable/Docker.dmg)

#### Beta Docker下载

废话就不多说了直接上下载链接：[https://download.docker.com/mac/beta/Docker.dmg](https://download.docker.com/mac/beta/Docker.dmg)

### 配置

安装完成后，在终端中输入docker会出现
![mac_docker_dz](/img/in-post/java_introduction/mac_docker_dz.png)

接着设置docker国内安装源
![ mac_docker_p](/img/in-post/java_introduction/mac_docker_p.png)
![mac_docker](/img/in-post/java_introduction/mac_docker.png)

**建议加入其他国内镜像**

```
https://registry.docker-cn.com
https://docker.mirrors.ustc.edu.cn
https://hub-mirror.c.163.com
```
官方镜像加速地址：[https://www.docker-cn.com/registry-mirror](https://www.docker-cn.com/registry-mirror)

### 使用
**在这里就不多介绍docker内容直接上docker文档**：[https://yeasy.gitbooks.io/docker_practice/content/](https://yeasy.gitbooks.io/docker_practice/content/)



