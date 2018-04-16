---
layout:     post
title:      "Docker-jenkins 安装及配置"
subtitle:   "Docker 安装配置 jenkins"
date:       2016-10-07 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-rwd.jpg"
catalog: true
tags:
    - Docker
    - Jenkins
--- 

## Docker 安装配置 jenkins

### 安装

```
docker run -p 8080:8080 -p 50000:50000 -d jenkins/jenkins:lts
```

### 进入容器获取秘钥

```
docker exec -it 容器名称/ID bash   
```

### 配置插件

**Publish Over SSH**

**Maven Integration**

**Gitlab Hook**

### 系统设置配置Publish Over SSH 地址

![img](/img/in-post/java_introduction/docker_jenkins_1.png)

### 配置项目

![img](/img/in-post/java_introduction/docker_jenkins_2.png)

![img](/img/in-post/java_introduction/docker_jenkins_3.png)

![img](/img/in-post/java_introduction/docker_jenkins_4.png)

![img](/img/in-post/java_introduction/docker_jenkins_5.png)

![img](/img/in-post/java_introduction/docker_jenkins_6.png)

**Exec代码**

```
cp /root/apache-tomcat-8.5.13/webapps/target/*.war /root/apache-tomcat-8.5.13/webapps/ROOT.war
/root/apache-tomcat-8.5.13/tomcat restart
```

**tomcat脚本-chomd 755 tomcat**

```
#!/bin/sh

# description: Auto-starts tomcat
# processname: tomcat
case $1 in
start)
sh /root/apache-tomcat-8.5.13/bin/startup.sh
;;
stop)
sh /root/apache-tomcat-8.5.13/bin/shutdown.sh
;;
restart)
sh /root/apache-tomcat-8.5.13/bin/shutdown.sh
sh /root/apache-tomcat-8.5.13/bin/startup.sh
;;
esac
exit 0

```

下面就可以构建项目查看数据内容啦










