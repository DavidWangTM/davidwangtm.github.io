---
layout:     post
title:      "分布式框架Tudou-Dubbo(二)"
subtitle:   "分布式框架Tudou(二) Dubbo工程框架搭建"
date:       2016-10-08 17:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Dubbo
    - Java
--- 

## Dubbo工程框架搭建

### Dubbo工程搭建业务模型说明

**由于公司业务急速增长，模块之间的关系越来越耦合，决定重组模块**

家装行业，程序的定义设计的目的:方便公司流程和数据管理，解耦业务之间的数据关系.

功能规划为：

- UPMS权限管理中心（用于基础权限配置和管理员数据查看和运维工作）
- OA管理系统 （用于公司的OA系统管理-里面设计包括Activi流程模块）
- CODE_GEN代码生成系统 (由于开发资源有限，避免重复的劳动力，创造通用轮子)
- SCHEDULER任务调度系统 （公司会有一些数据统计和数据报表，以及推送服务）
- OSS授权系统 （授权第三方的调用API，以及各系统之间的权限授权）
- APP管理系统 （用户APP后台数据配置管理）
- MES系统 (用户MES信息资源维护)
- CRM系统 （用户销售渠道管理-包括call center）
- IM系统 （最开始使用的是当当的开源TALK，由于开发资源有限后期已网易云，包括消息的管理和设置）
- PAY系统 （支付系统-对应第三方支付系统（支付宝，微信，银联等））
- PC-M-WEB系统 （官网和移动站基础信息配置）
- WMS系统 （供应链和仓储物流管理）
- 财务系统（方便公司财务材料结算）
- BM系统 （打算购买设计师的设计系统，目前暂定）

以上是根据业务模块拆分系统模块：系统管理配置如下：

- ZOOKEEPER 集群配置
- REDIS 集群配置
- NGINX 集群配置
- MYSQL 集群配置（读写分离）
- ACTIVEMQ 集群配置
- DOCKER-SWARM 集群配置

由于用户安全期不稳定，因此购买`阿里云`弹性服务器，可以伸缩容量。

### Dubbo工程数据模型创建

模型基础数据是以后台管理人员权限，和用户权限来划分，如下图：

![img](/img/in-post/java_introduction/dubbo_introduction_1.png)

- ucenter_oauth 用户权限信息表（包括用户第三方验证信息）
- ucenter_user 用户基础信息
- ucenter_user_details 用户二级维护数据
- ucenter_user_log 用户日志
- ucenter_user_oauth 用户授权

- upms_log 管理用户日志
- upms_markdown 管理用户的日志反馈
- upms_organization 组织信息
- upms_permission 权限信息
- upms_role 角色信息
- upms_role_permission 角色对应权限关系
- upms_system 系统管理
- upms_user 员工基础信息
- upms_user_organization 管理员工组织对应关系
- upms_user_permission 管理用户以及员工权限对应关系
- upms_user_role 管理用户以及员工角色对应关系

### Dubbo工程创建

**创建MAVEN工程，然后层层创建即可**

![img](/img/in-post/java_introduction/dubbo_introduction_2.png)

![img](/img/in-post/java_introduction/dubbo_introduction_4.png)

![img](/img/in-post/java_introduction/dubbo_introduction_3.png)










