---
layout:     post
title:      "分布式框架Tudou-Dubbo(一)"
subtitle:   "分布式框架Tudou(一) dubbo基础环境搭建"
date:       2016-10-08 17:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Docker
    - Dubbo
    - Compose
    - Java
--- 

## Docker-Compose 使用

使用分布式框架，需要的docker服务包括`zookeeper`,`dubbo-admin`,`mysql`,`redis`

1. `zookeeper`  dubbo 注册服务器中心
2. `dubbo-admin`  dubbo 服务管理平台
3. `mysql` dubbo 在本工程中服务器内持久化数据库
4. `redis` dubbo 在本工程中服务器内缓存数据库

为了方便管理这些容器，使用了 Compose ,如下：

```
version: "3"
services:
  producer:
    build: .
  zookeeper:
    image: 'zookeeper:latest'
    hostname: zookeeper
    ports:
        - "2181:2181"
  dubbo-admin:
    image: 'riveryang/dubbo-admin'
    ports:
        - "8061:8080"
    environment:
        DUBBO_REGISTRY: zookeeper:\/\/zookeeper:2181
  mysql:
    image: 'mysql:latest'
    hostname: mysql
    ports:
        - "3306:3306"
    environment:
        MYSQL_ROOT_PASSWORD: 123456
  redis:
    image: 'redis:latest'
    hostname: redis
    ports:
        - "6379:6379"

```

1. `build` 指定 Dockerfile 所在文件夹的路径（可以是绝对路径，或者相对 docker-compose.yml 文件的路径）。 Compose 将会利用它自动构建这个镜像，然后使用这个镜像（这个为了启动自己的DockerFile）。
2. `image` 指定为镜像名称或镜像 ID。如果镜像在本地不存在，Compose 将会尝试拉去这个镜像
3. `ports` 暴露端口信息。 使用宿主端口：容器端口 (HOST:CONTAINER) 格式，或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。
4. `environment` 设置环境变量。你可以使用数组或字典两种格式。只给定名称的变量会自动获取运行 Compose 主机上对应变量的值，可以用来防止泄露不必要的数据.
5. `hostname` 容器的主机名称

**运行安装完成后如下**

![img](/img/in-post/java_introduction/dubbo_docker_1.png)

