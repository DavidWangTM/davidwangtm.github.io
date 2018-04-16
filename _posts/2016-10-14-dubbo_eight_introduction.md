---
layout:     post
title:      "分布式框架Tudou-Dubbo(八)"
subtitle:   "分布式框架Tudou(八) Activiti工作流介绍"
date:       2016-10-14 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## Activiti 工作流介绍

### Activiti Maven 配置

```
	<dependency>
            <groupId>org.activiti</groupId>
            <artifactId>activiti-engine</artifactId>
            <version>${activiti.version}</version>
        </dependency>
        <dependency>
            <groupId>org.activiti</groupId>
            <artifactId>activiti-spring</artifactId>
            <version>${activiti.version}</version>
        </dependency>
        <dependency>
            <groupId>org.activiti</groupId>
            <artifactId>activiti-explorer</artifactId>
            <version>${activiti.version}</version>
            <exclusions>
                <exclusion>
                    <artifactId>vaadin</artifactId>
                    <groupId>com.vaadin</groupId>
                </exclusion>
                <exclusion>
                    <artifactId>dcharts-widget</artifactId>
                    <groupId>org.vaadin.addons</groupId>
                </exclusion>
                <exclusion>
                    <artifactId>activiti-simple-workflow</artifactId>
                    <groupId>org.activiti</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.activiti</groupId>
            <artifactId>activiti-modeler</artifactId>
            <version>${activiti.version}</version>
        </dependency>
        <dependency>
            <groupId>org.activiti</groupId>
            <artifactId>activiti-diagram-rest</artifactId>
            <version>${activiti.version}</version>
        </dependency>
```

### activiti.xml 配置

```
<!-- Activiti begin -->
    <bean id="processEngineConfiguration" class="org.activiti.spring.SpringProcessEngineConfiguration">
        <property name="dataSource" ref="dataSource" />
        <property name="transactionManager" ref="transactionManager" />
        <property name="databaseSchemaUpdate" value="true" /><!-- 自动建表，自动更新表结构 -->
        <property name="jobExecutorActivate" value="true" /><!-- 该参数将启用定时相关的功能 -->
        <property name="history" value="full" /><!-- 历史记录保存模式 -->

        <!-- UUID作为主键生成策略 -->
        <property name="idGenerator" ref="idGen" />

        <!-- 生成流程图的字体 -->
        <property name="activityFontName" value="simsun"/>
        <property name="labelFontName" value="simsun"/>
        <property name="annotationFontName" value="宋体"/><!-- 5.21.0 新增参数 -->

        <!-- 自定义用户权限 ，必须重新实现用户和组的相应方法-->
        <property name="customSessionFactories">
            <list>
                <bean class="com.tudou.oa.service.controller.act.ext.ActUserEntityServiceFactory">
                    <property name="actUserEntityService">
                        <bean class="com.tudou.oa.service.controller.act.ext.ActUserEntityService"/>
                    </property>
                </bean>
                <bean class="com.tudou.oa.service.controller.act.ext.ActGroupEntityServiceFactory">
                    <property name="actGroupEntityService">
                        <bean class="com.tudou.oa.service.controller.act.ext.ActGroupEntityService"/>
                    </property>
                </bean>
            </list>
        </property>
    </bean>

    <bean id="processEngineFactory" class="org.activiti.spring.ProcessEngineFactoryBean">
        <property name="processEngineConfiguration" ref="processEngineConfiguration" />
    </bean>

    <bean id="repositoryService" factory-bean="processEngineFactory" factory-method="getRepositoryService" />
    <bean id="runtimeService" factory-bean="processEngineFactory" factory-method="getRuntimeService" />
    <bean id="formService" factory-bean="processEngineFactory" factory-method="getFormService" />
    <bean id="identityService" factory-bean="processEngineFactory" factory-method="getIdentityService" />
    <bean id="taskService" factory-bean="processEngineFactory" factory-method="getTaskService" />
    <bean id="historyService" factory-bean="processEngineFactory" factory-method="getHistoryService" />
    <bean id="managementService" factory-bean="processEngineFactory" factory-method="getManagementService" />

    <!-- json处理
    <bean id="objectMapper" class="com.fasterxml.jackson.databind.ObjectMapper" />
    -->

    <!-- 设置默认的资源类型 -->
    <bean id="contentTypeResolver" class="org.activiti.rest.common.application.DefaultContentTypeResolver" />
    <bean id="idGen" class="com.tudou.common.util.IdGen" />
```

### Activiti 数据库说明

![img](/img/in-post/java_introduction/dubbo_introduction_34.png)

**Activiti所有的表都以ACT_开头。第二部分是表示表的用途的两个字母标识，用途也和服务的API对应。**

- ACT_RE_*: 'RE'表示repository。这个前缀的表包含了流程定义和流程静态资源 （图片，规则，等等）。

- ACT_RU_*: 'RU'表示runtime。这些运行时的表，包含流程实例，任务，变量，异步任务，等运行中的数据。 Activiti只在流程实例执行过程中保存这些数据， 在流程结束时就会删除这些记录。这样运行时表可以一直很小速度很快。

- ACT_ID_*: 'ID'表示identity。这些表包含身份信息，比如用户，组等等。

- ACT_HI_*: 'HI'表示history。这些表包含历史数据，比如历史流程实例， 变量，任务等等。

- ACT_GE_*: 通用数据，用于不同场景下，如存放资源文件。

### Activiti 使用

![img](/img/in-post/java_introduction/dubbo_introduction_33.png)

![img](/img/in-post/java_introduction/dubbo_introduction_35.png)

![img](/img/in-post/java_introduction/dubbo_introduction_36.png)

**如图所示：先创建模板或者导入模板(zip.bar.bpmn.bpmn20.xml)编辑完成后，会生成流程，可以启动和关闭流程，流程有版本号方便使用人员追溯**

### 对接 Activiti

**由于Activiti中只能自己填写用户ID或者用户姓名来关联用户属性，不方便，也容易产生后续一系列BUG存在，改写activiti中的用户和组织选择部分**

![img](/img/in-post/java_introduction/dubbo_introduction_37.png)

![img](/img/in-post/java_introduction/dubbo_introduction_38.png)

![img](/img/in-post/java_introduction/dubbo_introduction_39.png)




