---
layout:     post
title:      "Docker-Nexus 安装及配置"
subtitle:   "Docker 安装配置 Nexus"
date:       2016-10-07 18:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Docker
    - Nexus
--- 

## Docker 搭建maven私服

nexus 是 [sonatype](https://www.sonatype.com/) 下的Maven管理器。

docker-nexus3 是  [sonatype](https://github.com/sonatype/docker-nexus3)  下的docker开源容器库，只需要简单的使用docker命令即可创建nexus.

### Docker 镜像 docker-nexus3

官方有两种方式

1.  使用 docker 的 data volume （推荐）
2.  使用本地目录作为 container 的 volume

#### 使用 data volume

```
docker volume create --name nexus-data
docker run -d -p 8081:8081 --name nexus -v nexus-data:/nexus-data sonatype/nexus3
```
#### 使用本地目录

```
mkdir nexus
cd nexus
docker run -d -p 8081:8081 --name nexus -v $PWD/nexus-data:/nexus-data sonatype/nexus3
```
### 检测 nexus 服务器

```
curl -u admin:admin123 http://localhost:8081/service/metrics/ping
```
这里`admin:admin123` 是 nexus 服务器初始默认的用户名和密码

### 介绍 nexus 中的模块

进入容器后可以看到

![docker_nexus_1](/img/in-post/java_introduction/docker_nexus_1.png)

*   maven-central 是代理仓库，依赖在私服上不存在时，会直接从maven中央仓库中下载，并缓存到里面

*   maven-public 是仓库组，包括maven- releases,maven- snapshots,maven-central，新创建的仓库时会加入到maven-public的group中，只需要配置maven-public的mirror就可以使用用私服内的所有依赖

*   maven-releases和maven-snapshots分别对应着发布版和快照版

可以创建给开发人员用的用户，分给可以read、browse所有仓库的权限

### 使用 maven 私服

需要在 .m2目录下追加如下配置文件

**test用户是新创建用户，只有读取和浏览的依赖包的全选，需要上传到私服的用户（releases，snapshots）**

```
<servers>
	  <server>  
		  <id>public</id>  
		  <username>test</username>  
		  <password>123456</password>  
  	 </server>
	 <server>
		 <id>releases</id>
		 <username>admin</username>
		 <password>admin123</password>
	 </server>
	 <server>
		 <id>snapshots</id>
		 <username>admin</username>
		 <password>admin123</password>
	 </server>
</servers>

<mirrors>
	 <mirror>
		 <id>nexus</id>
		 <mirrorOf>*</mirrorOf>
		 <url>http://127.0.0.1:8081/repository/maven-public/</url>
	 </mirror>
</mirrors>

<profiles>
	 <profile>  
		 <id>dev</id>
		 <repositories>
			 <repository>
				 <id>Nexus</id>
				 <url>http://127.0.0.1:8081/repository/maven-public/</url>
				 <releases>
				 <enabled>true</enabled>
				 </releases>
				 <snapshots>
				 <enabled>true</enabled>
				 </snapshots>
			 </repository>
		 </repositories>
	 </profile>
</profiles>

<activeProfiles>
 	<activeProfile>dev</activeProfile>
</activeProfiles>
```
### 配置POM

```
<distributionManagement>
 <repository> 
	 <id>releases</id>
	 <name>Releases</name>
	 <url>http://127.0.0.1:8081/repository/maven-releases/</url>
 </repository> 
<snapshotRepository> 
	<id>snapshots</id>
	 <name>Snapshot</name>
	 <url>http://127.0.0.1:8081/repository/maven-snapshots/</url>
 </snapshotRepository> 
</distributionManagement>
```







