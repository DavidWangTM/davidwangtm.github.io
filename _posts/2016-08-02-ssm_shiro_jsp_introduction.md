---
layout:     post
title:      "SSM框架 shiro篇"
subtitle:   "Shiro 在jsp中的使用"
date:       2016-08-02 07:00:00
author:     "DavidWang"
header-img: "img/post-bg-js-module.jpg"
catalog: true
tags:
    - SSM
--- 

Shiro    提供了 JSP 的一套 JSTL 标签，用于做 JSP   页面做权限控制的。可以控制一些按钮和一些超链接，或者一些显示内容。

不过引入有区分，JSP需要在头部引入， Shiro  提供的TLD 文件:

```
<%@taglib prefix="shiro" uri="http://shiro.apache.org/tags" %>  
```

下面是具体的标签用法：

### 1.guest（游客）

```
<shiro:guest>  
您当前是游客，<a href="javascript:void(0);" class="dropdown-toggle qqlogin" >登录</a>
</shiro:guest> 
```

### 2.user（已经登录，或者记住我登录）

```
<shiro:user>  
欢迎[<shiro:principal/>]登录，<a href="/logout.shtml">退出</a>  
</shiro:user>   
```

### 3.authenticated（已经认证，排除记住我登录的）

```
<shiro:authenticated>  
	用户[<shiro:principal/>]已身份验证通过  
</shiro:authenticated>   
```

### 4.notAuthenticated（和authenticated相反）

```
<shiro:notAuthenticated>
	当前身份未认证（包括记住我登录的）
</shiro:notAuthenticated> 
```

这个功能主要用途，识别是不是本次操作登录过的，比如支付系统，进入系统可以用记住我的登录信息，但是当要关键操作的时候，需要进行认证识别。

### 5.principal标签，比较常用。

principal 标签，取值取的是你登录的时候。在Realm 实现类中的如下代码：

```
....
return new SimpleAuthenticationInfo(user,user.getPswd(), getName());
```

在new SimpleAuthenticationInfo(第一个参数,....) 的第一个参数放的如果是一个username ，那么就可以直接用。

```
<!--取到username-->
<shiro: principal/>
```

如果第一个参数放的是对象，比如我喜欢放User 对象。那么如果要取username 字段。

```
<!--需要指定property-->
<shiro:principal property="username"/>
```

和下面 Java  代码一致

```
User user = (User)SecurityUtils.getSubject().getPrincipals();
String username = user.getUsername();
```

### 6.hasRole标签（判断是否拥有这个角色）

```
<shiro:hasRole name="admin">  
	用户[<shiro:principal/>]拥有角色admin<br/>  
</shiro:hasRole>   
```

### 7.hasAnyRoles标签（判断是否拥有这些角色的其中一个）

```
<shiro:hasAnyRoles name="admin,user,member">  
用户[<shiro:principal/>]拥有角色admin或user或member<br/>  
</shiro:hasAnyRoles>   
```

### 8.lacksRole标签（判断是否不拥有这个角色）

```
<shiro:lacksRole name="admin">  
用户[<shiro:principal/>]不拥有admin角色
</shiro:lacksRole>   
```

### 9.hasPermission标签（判断是否有拥有这个权限）

```
<shiro:hasPermission name="user:add">  
	用户[<shiro:principal/>]拥有user:add权限
</shiro:hasPermission>   
```

### 10.lacksPermission标签（判断是否没有这个权限）

```
<shiro:lacksPermission name="user:add">  
	用户[<shiro:principal/>]不拥有user:add权限
</shiro:lacksPermission>   
```

