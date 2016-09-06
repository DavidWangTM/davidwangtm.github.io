---
layout:     post
title:      "Zookeeper 安装及配置"
subtitle:   "在 Mac 上安装配置 Zookeeper"
date:       2016-09-02 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-rwd.jpg"
catalog: true
tags:
    - Mac
    - Zookeeper
--- 

### Zookeeper 的安装

```
下载地址：http://www.apache.org/dyn/closer.cgi/zookeeper
```

解压文件：

```
tar zxvf zookeeper-3.4.8.tar.gz
```

### Zookeeper 的配置

#### 一、单机模式

##### 1.1、编辑配置文件
在“zookeeper-3.4.8”目录下，新建一个名为"data"和"logs"的文件，其中内容如下：

```
mkdir data
mkdir logs
```

```
tickTime=2000  
dataDir= /usr/local/zookeeper-3.4.8/data  (填写自己的data目录)  
dataLogDir=/usr/local/zookeeper-3.4.8/logs  
clientPort=2181
```

**参数说明:**

```
#tickTime: zookeeper中使用的基本时间单位, 毫秒值.
#dataDir: 数据目录. 可以是任意目录.
#dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和#dataDir相同的设置.
#clientPort: 监听client连接的端口号.
```

##### 1.2、运行ZooKeeper Server

执行./bin/zkServer.sh start命令，运行ZooKeeper Server进程，得到如图所示结果，表示后台运行ZooKeeper Server进程成功。

![IMG](/img/in-post/java_introduction/cmd_start.png)

注:命令telnet 127.0.0.1 2181 连接成功验证Zookeeper 是否启动成功
停止：./bin/zkServer.sh stop
也可以执行bin/zkServer.sh start-foreground命令，非后台运行ZooKeeper Server进程

![IMG](/img/in-post/java_introduction/cmd_foreground.png)

#### 二、集群模式

集群模式有两种形式：
1）使用多台机器，在每台机器上运行一个ZooKeeper Server进程；
2）使用一台机器，在该台机器上运行多个ZooKeeper Server进程。
在生产环境中，一般使用第一种形式，在练习环境中，一般使用第二种形式。

##### 2.1、参数配置

集群模式下，需要配置一些参数，以下是常见的一些参数。

1. data目录,用于存放进程运行数据。
2. data目录下的myid文件,用于存储一个数值，用来作为该ZooKeeper Server进程的标识。
3. 监听Client端请求的端口号
4. 监听同ZooKeeper集群内其他Server进程通信请求的端口号
5. 监听ZooKeeper集群内“leader”选举请求的端口号,该端口号用来监听ZooKeeper集群内“leader”选举的请求。注意这个是ZooKeeper集群内“leader”的选举，跟分布式应用程序无关。

**参数配置注意事项：**

1. 同一个ZooKeeper集群内，不同ZooKeeper Server进程的标识需要不一样，即myid文件内的值需要不一样
2. 采用上述第2种形式构建ZooKeeper集群，需要注意“目录，端口号”等资源的不可共享性，如果共享会导致ZooKeeper Server进程不能正常运行，比如“data目录，几个监听端口号”都不能被共享

```
//先在zookeeper-3.4.6创建 
mkdir data
mkdir logs
//然后
cp zookeeper-3.4.6 zookeeper-3.4.6_1
cp zookeeper-3.4.6 zookeeper-3.4.6_2
cp zookeeper-3.4.6 zookeeper-3.4.6_3
//分别在3个目录的conf中创建zoo.cfg
```

三个zoo.cfg配置如下：

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/usr/local/zookeeper-3.4.6_1/data
dataLogDir=/usr/local/zookeeper-3.4.6_1/logs
clientPort=2181

server.1=192.168.1.106:2881:3881
server.2=192.168.1.106:2882:3882
server.3=192.168.1.106:2883:3883

tickTime=2000
initLimit=10
syncLimit=5
dataDir=/usr/local/zookeeper-3.4.6_2/data
dataLogDir=/usr/local/zookeeper-3.4.6_2/logs
clientPort=2182

server.1=192.168.1.106:2881:3881
server.2=192.168.1.106:2882:3882
server.3=192.168.1.106:2883:3883

tickTime=2000
initLimit=10
syncLimit=5
dataDir=/usr/local/zookeeper-3.4.6_3/data
dataLogDir=/usr/local/zookeeper-3.4.6_3/logs
clientPort=2183

server.1=192.168.1.106:2881:3881
server.2=192.168.1.106:2882:3882
server.3=192.168.1.106:2883:3883
```

**参数说明:**

```
tickTime=2000
tickTime这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔,也就是每个tickTime时间就会发送一个心跳。

initLimit=10
initLimit这个配置项是用来配置Zookeeper接受客户端（这里所说的客户端不是用户连接Zookeeper 服务器的客户端,而是Zookeeper服务器集群中连接到Leader的Follower 服务器）初始化连接时最长 能忍受多少个心跳时间间隔数。当已经超过 10 个心跳的时间（也就是 tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息,那么表明这个客户端连接失败。总的时间长度就是 10*2000=20 秒。

syncLimit=5
syncLimit 这个配置项标识Leader与Follower之间发送消息,请求和应答时间长度,最长不能超过多少个tickTime的时间长度,总的时间长度就是5*2000=10秒。

dataDir=/usr/local/zookeeper-3.4.6_(x)/data
dataDir顾名思义就是Zookeeper保存数据的目录,默认情况下Zookeeper将写数据的日志文件也保存在这个目录里。

clientPort=2181
clientPort这个端口就是客户端（应用程序）连接Zookeeper服务器的端口,Zookeeper 会监听这个端 口接受客户端的访问请求。

server.A=B：C：D
server.1=192.168.1.106:2881:3881
server.2=192.168.1.106:2882:3882
server.3=192.168.1.106:2883:3883
A 是一个数字,表示这个是第几号服务器；
B 是这个服务器的IP地址（或者是与IP地址做了映射的主机名）；
C 第一个端口用来集群成员的信息交换,表示这个服务器与集群中的 Leader 服务器交换信息的端口；
D 是在leader挂掉时专门用来进行选举 leader 所用的端口。
```

在data目录下创建myid,如下：

```
$ vi /usr/local/zookeeper-3.4.6_1 /data/myid 设置值为1
$ vi /usr/local/zookeeper-3.4.6_2 /data/myid 设置值为2
$ vi /usr/local/zookeeper-3.4.6_3 /data/myid 设置值值为3
```

##### 2.2、启动并查看zookeeper:

```
$ /usr/local/zookeeper-3.4.6_x/bin/zkServer.sh start
$ /usr/local/zookeeper-3.4.6_x/bin/zkServer.sh status
```

##### 2.3、另外的配置方法

在zookeeper-3.4.6中创建/wx/data,如下:

| myid | Data目录 | Client | Server | Leader|
| 1 | /w1/data | 2181 | 2881 | 3881 |
| 2 | /w2/data | 2182 | 2882 | 3882 |
| 3 | /w3/data | 2183 | 2883 | 3883 |

配置如下:

```
# zx.cfg  
tickTime=2000  
initLimit=10  
syncLimit=2  
dataDir=/usr/myenv/zookeeper-3.4.6/wx/data  
clientPort=218x  
# server.x中的“x”表示ZooKeeper Server进程的标识  
server.1=192.168.1.106:2881:3881 
server.2=192.168.1.106:2882:3882 
server.3=192.168.1.106:2883:3883
```

运行如下:

```
bin/zkServer.sh start deploy/w1/w1.cfg，  
bin/zkServer.sh start deploy/w2/w2.cfg  
bin/zkServer.sh start deploy/w3/w3.cfg
```



