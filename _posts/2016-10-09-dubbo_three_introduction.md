---
layout:     post
title:      "分布式框架Tudou-Dubbo(三)"
subtitle:   "分布式框架Tudou(三) MAVEN 配置"
date:       2016-10-09 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## Dubbo MAVEN 配置

### Dubbo 工程 Maven 设计

**由于模块代码专注所在业务代码内，无需查看其它服务代码，对接只需要调用对应的服务即可，提取Maven配置在公共配置中，可以节约空间，并且解耦**


- 在主工程中引用 dependencyManagement 控制Maven引用库的版本。
- 在配置工程中(tudou-common)引用 dependencies 加载通用 Maven 引用库。

![img](/img/in-post/java_introduction/dubbo_introduction_5.png)

![img](/img/in-post/java_introduction/dubbo_introduction_6.png)

**由于考虑到单模块也有自己的特性，也有通用引入库，将单模块创建 common 引入 tudou-common**

![img](/img/in-post/java_introduction/dubbo_introduction_7.png)

### Dubbo 各模块 Maven 的提交管理

为了各个业务模块的相互解耦调用，使用库上传私服Maven，保证相互直接不干扰，详情操作可以查看Nexus的配置。

![img](/img/in-post/java_introduction/dubbo_introduction_8.png)

### Dubbo 各模块 开发版本管理

**为了统一环境配置，引入Maven profiles**

![img](/img/in-post/java_introduction/dubbo_introduction_9.png)

### Dubbo 调试

**加快开发调试，使用jetty插件和jrebel插件快速调试开发**

![img](/img/in-post/java_introduction/dubbo_introduction_10.png)

![img](/img/in-post/java_introduction/dubbo_introduction_11.png)

![img](/img/in-post/java_introduction/dubbo_introduction_12.png)








