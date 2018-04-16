---
layout:     post
title:      "Spring回顾 (八)"
subtitle:   "Spring MVC 和 struts2 对比"
date:       2016-11-17 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Spring
--- 

# Spring MVC 和 struts2 对比

## 框架机制

- Struts2采用Filter（StrutsPrepareAndExecuteFilter）实现，SpringMVC（DispatcherServlet）则采用Servlet实现。

### **servlet**

servlet是一种运行服务器端的java应用程序，具有独立于平台和协议的特性，并且可以动态的生成web页面，它工作在客户端请求与服务器响应的中间层。

### **Filter**

filter是一个可以复用的代码片段，可以用来转换HTTP请求、响应和头信息。Filter不像Servlet，它不能产生一个请求或者响应，它只是修改对某一资源的请求，或者修改从某一的响应。
可以在servlet之前，也可以在之后。


## 拦截机制

### **Struts2**

- Struts2框架是类级别的拦截，每次请求就会创建一个Action，和Spring整合时Struts2的ActionBean注入作用域是原型模式prototype（否则会出现线程并发问题），然后通过setter，getter吧request数据注入到属性。

- Struts2中，一个Action对应一个request，response上下文，在接收参数时，可以通过属性接收，这说明属性参数是让多个方法共享的。

- Struts2中Action的一个方法可以对应一个url，而其类属性却被所有方法共享，这也就无法用注解或其他方式标识其所属方法了

### **SpringMVC**

- SpringMVC是方法级别的拦截，一个方法对应一个Request上下文，所以方法直接基本上是独立的，独享request，response数据。而每个方法同时又何一个url对应，参数的传递是直接注入到方法中的，是方法所独有的。处理结果通过ModeMap返回给框架。

- 在Spring整合时，SpringMVC的Controller Bean默认单例模式Singleton，所以默认对所有的请求，只会创建一个Controller，有应为没有共享的属性，所以是线程安全的，如果要改变默认的作用域，需要添加@Scope注解修改。

### 性能方面

SpringMVC实现了零配置，由于SpringMVC基于方法的拦截，有加载一次单例模式bean注入。而Struts2是类级别的拦截，每次请求对应实例一个新的Action，需要加载所有的属性值注入，所以，SpringMVC开发效率和性能高于Struts2。

### 拦截机制

Struts2有自己的拦截Interceptor机制，SpringMVC这是用的是独立的Aop方式，这样导致Struts2的配置文件量还是比SpringMVC大。

### 配置方面

Spring MVC和Spring是无缝的。从这个项目的管理和安全上也比Struts2高（当然Struts2也可以通过不同的目录结构和相关配置做到SpringMVC一样的效果，但是需要xml配置的地方不少）。
SpringMVC可以认为已经100%零配置。

### 设计思想

Struts2更加符合OOP的编程思想， SpringMVC就比较谨慎，在servlet上扩展。

### 集成方面

SpringMVC集成了Ajax，使用非常方便，只需一个注解@ResponseBody就可以实现，然后直接返回响应文本即可，而Struts2拦截器集成了Ajax，在Action中处理时一般必须安装插件或者自己写代码集成进去，使用起来也相对不方便