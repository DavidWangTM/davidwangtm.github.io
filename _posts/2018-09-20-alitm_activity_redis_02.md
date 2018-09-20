---
layout:     post
title:      "Redis 持久化"
subtitle:   "Redis 持久化 - RDB | AOF"
date:       2018-09-20 10:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Redis
--- 


redis持久化
===

今天聊天被问到`redis`的持久化，很长时间没接触也未使用，此文帮助自己做持久化的笔记。

查了官方的文档和大博主的说明`redis`主要实现持久模式有2种

## 快照 

- mysql Dump

- redis RDB

## 写日志

- mysql binlog

- redis AOF

mysql 模式不太常用，今天列出 RDB 和 AOF

### RDB - 快照模式-默认持久化机制

#### 主要流程

1. redis内存
2. 创建快照
3. 生成RDB文件
4. 重新载入
5. redis内容

#### 那些情况会执行快照

1. 服务器正常关闭会生成快照

2. 当key满足一定条件的情况下会生成快照。

```
//编辑 `redis.config` 在60秒内，更改键大于1次时候就保存快照
save 60 1
```

3. 当执行 `save`(同步) 和 `bgsave`(异步) 指令会产生快照

	1.Sava （同步） 
	redis: save #(可能会阻塞 redis) 如果存在老的RDB文件，将会替换
	
	2.bgsave (异步 不会阻塞redis,但是fork()新进程）先返回OK ,在后台继续执行 
	执行过程：通过fork().生成子进程，CreateRDB去生成RDB 文件，告诉主线程生成成功

| 命令 | save | bgsave |
|---|---|---|
| IO类型 | 同步 | 异步 |
| 阻塞 | 是 | 是 |
| 复杂度 | O(n) | o(n) |
| 优点 | 不会消耗额外内存 | 不阻塞客户端命令 |
| 缺点 | 阻塞客户端 | 需要fork,消耗内存 |

#### 配置说明

```
/**
dbfilename dump.dp #文件名称 一般 dump-{port}.rdb 
dir./ #存放在当前目录下 
stop-writes-on-bgsave-error yes #出现错误是否停止写 
rdbcompression yes #是否压缩 
rdbchecksum yes #校验
**/

cp redis.conf redis-test.conf

vi redis-test.conf

[ 
daemonize yes 
logfile “test.log” 
dbfilename dump-test.rdb 
dir /usr/local/redis/data 
]

redis-server redis-test.conf

在日志界面就可以看到内容了 /usr/local/redis/data

tail -f test.log

```
### AOF-日志模式

#### 主要流程

1.创建 - 写入一条命令 同步到日志文件中

2.恢复 - AOF将日志文件写入到 redis中同步数据

#### AOF 三种策略

1.always 
写命令时候写到硬盘的缓冲区，根据一定的策略写入到磁盘当中（AOF) 
always 是每一条命令到会写入到硬盘中（1s内可能写入很多次） 

2.everysec 
每秒把缓冲区写到缓冲值，高写入量时候保护磁盘 

3.no 
操作系统决定什么时候该刷入硬盘


| 命令 | always | everysec | no |
|---|---|---|---|
| 优点 | 不丢失数据 | 每秒一次fsync | --- |
| 缺点 | IO开销大 | 丢一秒数据 | 不可控 |

#### 配置说明

```
/**
AOF重写 对原生AOF 进行优化，比如过期的，重复的，没有用的可以优化的命令重写。 
优点：减少磁盘占用量；加速恢复速度 
两种方式： 
1.bgrewriteaof 
类似bgsave ,子进程进行AOF重写（将radis 内存数据回溯） 
2.AOF重写配置（以下两个要求都需要满足） 
auto-aof-rewrite-min-size:AOF文件重写需要的尺寸 
auto-aof-rewrite-percentage:AOF文件增长率
**/

appendonly yes# 使用AOF 持久化基础 
appendfilename “appendonly-{port}.aof” # 持久化文件名 
appendfsync everysec #同步策略 
dir /path #日志路径 
no-appendfsync-on-rewrite yes# AOF 重写时候是否需要做append 操作，这里是不做

redis-cli 
config get appendonly 
config set appendonly yes 
config rewrite 
ll #data 下面出现 appendonly.aof 
bgrewriteaof # aof重写	
```

### RDB 和 AOF 选择

| 命令 | RDB | AOF |
|---|---|---|
| 启动优先级 | 低 | 高 |
| 体积 | 小 | 大 |
| 恢复速度 | 快 | 慢 |
| 数据安全性 | 丢数据 | 策略决定 |
| 轻重 | 重 | 轻 |


### 容灾备份

Redis 的容灾备份基本上就是对数据进行备份， 并将这些备份传送到多个不同的外部数据中心。

容灾备份可以在 Redis 运行并产生快照的主数据中心发生严重的问题时， 仍然让数据处于安全状态。

因为很多 Redis 用户都是创业者， 他们没有大把大把的钱可以浪费， 所以下面介绍的都是一些实用又便宜的容灾备份方法：

*   Amazon S3 ，以及其他类似 S3 的服务，是一个构建灾难备份系统的好地方。 最简单的方法就是将你的每小时或者每日 RDB 备份加密并传送到 S3 。 对数据的加密可以通过 `gpg -c` 命令来完成（对称加密模式）。 记得把你的密码放到几个不同的、安全的地方去（比如你可以把密码复制给你组织里最重要的人物）。 同时使用多个储存服务来保存数据文件，可以提升数据的安全性。
*   传送快照可以使用 SCP 来完成（SSH 的组件）。 以下是简单并且安全的传送方法： 买一个离你的数据中心非常远的 VPS ， 装上 SSH ， 创建一个无口令的 SSH 客户端 key ， 并将这个 key 添加到 VPS 的 authorized_keys 文件中， 这样就可以向这个 VPS 传送快照备份文件了。 为了达到最好的数据安全性，至少要从两个不同的提供商那里各购买一个 VPS 来进行数据容灾备份。

需要注意的是， 这类容灾系统如果没有小心地进行处理的话， 是很容易失效的。

最低限度下， 你应该在文件传送完毕之后， 检查所传送备份文件的体积和原始快照文件的体积是否相同。 如果你使用的是 VPS ， 那么还可以通过比对文件的 SHA1 校验和来确认文件是否传送完整。

另外， 你还需要一个独立的警报系统， 让它在负责传送备份文件的传送器（transfer）失灵时通知你。
