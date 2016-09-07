---
layout:     post
title:      "Acitivemq 安装及配置"
subtitle:   "在 MAC 系统安装 acitivemq"
date:       2016-09-03 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-rwd.jpg"
catalog: true
tags:
    - Mac
    - Acitivemq
--- 

## MAC系统安装acitivemq

### 安装

官方推荐使用brew安装，前提是Mac要先安装brew，也可以手动下载压缩包解压安装，如果已经安装了brew很简单，只需要下面一句话，安装位置：/usr/local/Cellar/activemq/5.13.3

```
$ brew install apache-activemq
$ cd /usr/local/Cellar/activemq/5.13.3
```

查看信息:

```
$ bin/activemq
```

配置activemq运行环境，默认路径/etc/default/activemq 或 /Users/user/.activemqrc

```
activemq setup /Users/apple/.activemqrc
```

activemq官方建议设置访问权限，这一步是可选的：

```
$chown 'apple':nogroup '/Users/apple/.activemqrc'
$chmod 600 '/Users/apple/.activemqrc'
```

### 启动

前台启动：

```
$ activemq console   
```

后台启动：

```
$ activemq start
```

指定运行日志输出到指定文件启动：

```
$ activemq start > /tmp/smlog  2>&1 & 
```

查看是否启动成功，查看端口61616是否运行成功：

```
$ netstat -an|grep 61616
```

或者浏览器访问activemq管理控制台：[http://localhost:8161/admin/](http://localhost:8161/admin/)，用户名和密码为`admin:admin`

### 停止

```
$ activemq stop
```

或者：

```
$ ps -ef|grep activemq
$ kill -9 pid
```

### 其他资料

**activemq使用文档：**

[http://activemq.apache.org/using-activemq-5.html](http://activemq.apache.org/using-activemq-5.html)

**activemq jndi支持：**

[http://activemq.apache.org/jndi-support.html](http://activemq.apache.org/jndi-support.html)

**activemq spring支持：**

[http://activemq.apache.org/spring-support.html](http://activemq.apache.org/spring-support.html)

**使用spring JMS：**

[http://docs.spring.io/spring/docs/2.5.x/reference/jms.html#jms-mdp](http://docs.spring.io/spring/docs/2.5.x/reference/jms.html#jms-mdp)




