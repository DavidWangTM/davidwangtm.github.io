---
layout:     post
title:      "Spring回顾 (六)"
subtitle:   "Spring MVC 九大组件"
date:       2016-11-15 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Spring
--- 

## Spring MVC 原理介绍

Spring MVC 本质是一个Servlet , 通过下图可以看出 Servlet 继承自 HttpServlet, 提供了： Servlet：HttpServletBean ，FrameworkServlet ，DispatcherServlet 继承关系.

![basic_design_patterns_28](/img/in-post/basic_design_patterns/basic_design_patterns_28.png)

- HttpServletBean直接继承自java的HttpServlet，其作用是将Servlet中配置的参数设置到相应的属性；

- FrameworkServlet 初始化了 WebApplicationContext，DispatcherServlet 初始化了自身的9个组件。

我们需要先了解Handler就是处理器，可以表现的是类，和方法，也可以理解为 Controller层中@RequestMapping标注的所有方法 , 只要能实际处理请求的都可以是Handler.

### HandlerMapping

对于`HandlerMapping`来说，其作用就是根据`request`找到相应的处理器`Handler`和`Intecepter`拦截器

```
HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception;
```

### HandlerAdapter

`Handler`是用来干活的工具,`HandlerMapping`用于根据需要干的活找到相应的工具,`HandlerAdapter`是使用工具干活的人。

```
public interface HandlerAdapter {
    boolean supports(Object var1);
    ModelAndView handle(HttpServletRequest var1, HttpServletResponse var2, Object var3) throws Exception;
    long getLastModified(HttpServletRequest var1, Object var2);
}

```
在`HandlerAdapter`接口中提供了`handle`这样一个方法，参数中Object handler第三个参数其实就是一个处理器，`handle`方法就是使用`handler`来处理逻辑的。处理之后返回一个`ModelAndView`。

### HandlerExceptionResolver

这个是`SpringMVC`中的异常处理组件，`HandlerExceptionResolver`这个组件的作用就是根据异常设置`ModelAndView`，然后再将处理结果交给`render`方法进行渲染，`HandlerExceptionResolver`是在渲染之前工作的，渲染过程中发生异`HandlerExceptionResolver`无法处理。

```
public interface HandlerExceptionResolver { 
	ModelAndView resolveException(HttpServletRequest var1,HttpServletResponse var2, Object var3, Exception var4); 
}
```

### ViewResolver

用来将 String 类型的视图名和 Locale 解析为 View 类型的视图。

```
View resolveViewName(String viewName, Locale locale) throws Exception;
```
- viewName 视图名称

- local 区域，作为国际化

### RequestToViewNameTranslator

获取 request 中的视图名。接口里面也是只有一个方法：

```
String getViewName(HttpServletRequest request) throws Exception; //根据 request 查找视图名
```

### LocaleResolver

`LocaleResolver` 用于从 request 解析出 Locale。

```
public interface LocaleResolver { 
	//从 request 解析出 Locale
	Locale resolveLocale(HttpServletRequest request); 
	//根据 request 设置 Locale
	void setLocale(HttpServletRequest request, HttpServletResponse response, Locale local); 
}
```

### ThemeResolver

解析主题，SpringMVC 中一个主题对应一个properties文件，里面存放着跟当前主题相关的所有资源、如图片、css样式。 SpringMVC 的主题也支持国际化，同一个主题不同区域也可以显示不同的风格。SpringMVC 中跟主题相关的类有 `ThemeResolver`、`ThemeSource` 和 `Theme`。主题是通过一系列资源来具体体现的，要得到一个主题的资源，首先要得到资源的名称，这是ThemeResolver的工作。然后通过主题名称找到对应的主题（可以理解为一个配置）文件，这是ThemeSource的工作流程。

```
public interface ThemeResolver {
 //通过给定的 request 查找主题名
 String resolveThemeName(HttpServletRequest request);
 //根据给定的 request 设置主题名
 void setThemeName(HttpServletRequest request, HttpServletResponse response, String themeName);
}
```

### MultipartResolver

`MultipartResolver` 用来处理上传请求 ，将request包装成`MultipartHttpServletRequest`。可以用`MultipartHttpServletRequest` 直接调用getFile获取文件。

```
public interface MultipartResolver {
 //根据 request 判断是否是上传请求
 boolean isMultipart(HttpServletRequest request);
 //将 request 包装成 MultipartHttpServletRequest
 MultipartHttpServletRequest resolveMultipart(HttpServletRequest request) throws MultipartException;
 //清理上传过程中产生的临时资源
 void cleanupMultipart(MultipartHttpServletRequest request);
}
```

### FlashMapManager

FlashMap 主要在 redirect 中传递参数，FlashMapManager 用来管理 FlashMap 。

```
public interface FlashMapManager {
 //恢复参数，并将恢复过的和超时的参数从保存介质中删除
 @Nullable
 FlashMap retrieveAndUpdate(HttpServletRequest request, HttpServletResponse response);
 //将参数保存起来
 void saveOutputFlashMap(FlashMap flashMap, HttpServletRequest request, HttpServletResponse response);
}
```
