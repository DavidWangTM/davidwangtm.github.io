---
layout:     post
title:      "分布式框架Tudou-Dubbo(四)"
subtitle:   "分布式框架Tudou(四) Dubbo 配置"
date:       2016-10-10 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

### Dubbo 配置

**详细配置这里不多介绍，直接上工程内配置的说明，其他的详见链接说明**


![img](/img/in-post/java_introduction/dubbo_introduction_13.png)

![img](/img/in-post/java_introduction/dubbo_introduction_14.png)

**详细文档请参考：**

- [Dubbo GITHUB地址](https://github.com/alibaba/dubbo)
- [Dubbo用户手册(中文)](http://dubbo.io/books/dubbo-user-book/)
- [Dubbo开发手册(中文) ](http://dubbo.io/books/dubbo-dev-book/)
- [Dubbo管理手册(中文)](http://dubbo.io/books/dubbo-admin-book/)

为了方便服务的监管和运维，`Dubbo`提供了`Dubbo-admin`:

- 可以去GITHUB地址上下载War包，部署tomcat即可 [Dubbo-admin]((https://github.com/alibaba/dubbo))
- 也可以使用Docker 镜像 :
```docker run -d --name my-dubbo-admin -p 7070:8080 --link my-zookeeper:zk -e DUBBO_REGISTRY="zookeeper:\/\/zk:2181" riveryang/dubbo-admin```
- --link 连接容器 参数说明： dokcer容器名称(或ID) : 名称 


默认账号密码为 root root ，安装好后，访问如下：

![img](/img/in-post/java_introduction/dubbo_introduction_15.png)

![img](/img/in-post/java_introduction/dubbo_introduction_16.png)

![img](/img/in-post/java_introduction/dubbo_introduction_17.png)

![img](/img/in-post/java_introduction/dubbo_introduction_18.png)

