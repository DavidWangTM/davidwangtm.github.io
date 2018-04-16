---
layout:     post
title:      "Spring回顾 (三)"
subtitle:   "BeanFactory 和 FactoryBean 区别"
date:       2016-11-12 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Spring
--- 

## 一概述

BeanFactory 与 FactoryBean的区别， 两个名字很像，面试中也经常遇到，所以容易搞混，现从源码以及示例两方面来分析。

## 二、源码

### BeanFactory

`BeanFactory` 定义了 IOC 容器的最基本形式，并提供了 IOC 容器应遵守的的最基本的接口，也就是 Spring IOC 所遵守的最底层和最基本的编程规范。

BeanFactory仅是个接口，并不是IOC容器的具体实现，具体的实现有：如 DefaultListableBeanFactory 、 XmlBeanFactory 、 ApplicationContext 等。

在 IOC 的说明有指出。[https://davidwangtm.github.io/2016/11/10/spring_one_introduction/](https://davidwangtm.github.io/2016/11/10/spring_one_introduction/)

### FactoryBean

`FactoryBean`工厂类接口，用户可以通过实现该接口定制实例化 Bean 的逻辑。

```
public  interface FactoryBean<T> { 
	//FactoryBean 创建的 Bean 实例  
	T getObject() throws Exception; 
	//返回 FactoryBean 创建的 Bean 类型 
	Class<?> getObjectType(); 
	//返回由 FactoryBean 创建的 Bean 实例的作用域是 singleton 还是 prototype  
	boolean isSingleton(); 
}
```

## 三、示例

```
public class Message{
	private String msg;
	
	public Message(String msg){
		this.msg = msg;
	}

	public void send(){
		System.out.println(msg);
	}
}
```


```
public class MessageBean implements  FactoryBean<Message>{
	
	public Message getObject() throws Exception { 
		return new Message("MessageBean Send"); 
	}

	public Class<?> getObjectType() {
	 	return MessageBean.class; 
	} 
	public boolean isSingleton() {
		 return  false; 
	}
}
```


```
<bean id="msg" class="com.test.Message" > 
	<constructor-arg value="Message Send"/> 
</bean> 
<bean id="msgBean"  class="com.test.MessageBean" />
```
```
public void testBean() throws Exception {
    ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");

    Message msg_one = (Message) ctx.getBean("msg");
    msg_one.send();

    Message mes_two = (Message) ctx.getBean("msgBean");
    mes_two.send();

    //使用&前缀可以获取FactoryBean本身
    FactoryBean dogFactoryBean = (FactoryBean) ctx.getBean("&msgBean");
    Message Message_three = (Message) dogFactoryBean.getObject();
    Message_three.send();
}

Message Send
MessageBean Send 
MessageBean Send

```

### 四、总结

通过以上源码和示例来看，基本上能印证以下结论，也就是二者的区别。

1.  BeanFactory是个Factory，也就是 IOC 容器或对象工厂，所有的 Bean 都是由 BeanFactory( 也就是 IOC 容器 ) 来进行管理。

2.  FactoryBean是一个能生产或者修饰生成对象的工厂Bean，可以在IOC容器中被管理，所以它并不是一个简单的Bean。当使用容器中factory bean的时候，该容器不会返回factory bean本身，而是返回其生成的对象。