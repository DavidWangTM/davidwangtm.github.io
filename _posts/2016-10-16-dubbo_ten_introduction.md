---
layout:     post
title:      "分布式框架Tudou-Dubbo(十)"
subtitle:   "分布式框架Tudou(十) Scheduler"
date:       2016-10-14 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## Scheduler

### Scheduler 配置

**applicationContext-quartz.xml**

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
	http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd"
       default-lazy-init="false" default-autowire="byName">

    <bean id="schedulerFactoryBean"	class="org.springframework.scheduling.quartz.SchedulerFactoryBean">
        <property name="dataSource">
            <ref bean="dataSource" /><!-- 数据源引用指向，包含集群所需的所有12张表 -->
        </property>
        <property name="configLocation" value="classpath:quartz.properties" />
    </bean>
</beans> 
```

**quartz.properties**

```
#============================================================================
# Configure Main Scheduler Properties  
#============================================================================
org.quartz.scheduler.instanceName = DefaultQuartzScheduler
org.quartz.scheduler.instanceId = AUTO
org.quartz.scheduler.rmi.export = false
org.quartz.scheduler.rmi.proxy = false
org.quartz.scheduler.wrapJobExecutionInUserTransaction = false

#============================================================================
# Configure ThreadPool  
#============================================================================
org.quartz.threadPool.class = org.quartz.simpl.SimpleThreadPool
org.quartz.threadPool.threadCount = 10
org.quartz.threadPool.threadPriority = 5
org.quartz.threadPool.threadsInheritContextClassLoaderOfInitializingThread = true

#============================================================================
# Configure JobStore  
#============================================================================
org.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
org.quartz.jobStore.tablePrefix = QRTZ_
org.quartz.jobStore.isClustered = true
org.quartz.jobStore.clusterCheckinInterval = 20000
#org.quartz.jobStore.dataSource = myDS
org.quartz.jobStore.maxMisfiresToHandleAtATime = 1
org.quartz.jobStore.misfireThreshold = 120000
org.quartz.jobStore.txIsolationLevelSerializable = true

#==============================================================
#Skip Check Update
#update:true
#not update:false
#==============================================================
org.quartz.scheduler.skipUpdateCheck = true

#============================================================================
# Configure Plugins
#============================================================================
org.quartz.plugin.triggHistory.class = org.quartz.plugins.history.LoggingJobHistoryPlugin
org.quartz.plugin.shutdownhook.class = org.quartz.plugins.management.ShutdownHookPlugin
org.quartz.plugin.shutdownhook.cleanShutdown = true
#============================================================================

# Configure DataSource
#============================================================================
#org.quartz.dataSource.myDS.driver = com.mysql.jdbc.Driver
#org.quartz.dataSource.myDS.URL = jdbc:mysql://localhost:3306/adminlte?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&failOverReadOnly=false&zeroDateTimeBehavior=convertToNull
#org.quartz.dataSource.myDS.user = users
#org.quartz.dataSource.myDS.password = admin
#org.quartz.dataSource.myDS.maxConnections = 10

```

以上配置好后数据库会生成：

![img](/img/in-post/java_introduction/dubbo_introduction_46.png)

### Scheduler 界面

**可以控制开始和结束，以及恢复删除和运行一次**

![img](/img/in-post/java_introduction/dubbo_introduction_47.png)

**tigger分为2中类型-SimpleTrigger-CronTrigger**

![img](/img/in-post/java_introduction/dubbo_introduction_48.png)

![img](/img/in-post/java_introduction/dubbo_introduction_49.png)

### Scheduler参考文档

[具体操作查看Scheduler文档](https://www.ibm.com/developerworks/cn/java/j-quartz/)

[Scheduler](http://www.quartz-scheduler.org/)
