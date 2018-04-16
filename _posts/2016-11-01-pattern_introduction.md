---
layout:     post
title:      "常用设计模式(一)"
subtitle:   "Proxy代理模式"
date:       2016-11-01 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - 设计模式
--- 

Proxy代理模式
===
在代理模式（Proxy Pattern）中，一个类代表另一个类的功能。这种类型的设计模式属于结构型模式。

在代理模式中，我们创建具有现有对象的对象，以便向外界提供功能接口。

## 介绍

- **意图：** 为其他对象提供一种代理以控制对这个对象的访问。

- **主要解决：** 在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因（比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问），直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。

- **何时使用：** 想在访问一个类时做一些控制。

-**如何解决：** 增加中间层。

-**关键代码：** 实现与被代理类组合。

-**应用实例：** 1.买火车票不一定在火车站买，也可以去代售点。 2.一张支票或银行存单是账户中资金的代理。支票在市场交易中用来代替现金，并提供对签发人账号上资金的控制。

-**优点：** 1、职责清晰。 2、高扩展性。 3、智能化。

-**缺点：** 1、由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。 2、实现代理模式需要额外的工作，有些代理模式的实现非常复杂。

-**使用场景：** 按职责来划分，通常有以下使用场景： 1、远程代理。 2、虚拟代理。 3、Copy-on-Write 代理。 4、保护（Protect or Access）代理。 5、Cache代理。 6、防火墙（Firewall）代理。 7、同步化（Synchronization）代理。 8、智能引用（Smart Reference）代理。

## 实现
我们将创建一个 _Image_ 接口和实现了 _Image_ 接口的实体类。_ProxyImage_ 是一个代理类，减少 _RealImage_ 对象加载的内存占用。

_ProxyPatternDemo_，我们的演示类使用 _ProxyImage_ 来获取要加载的 _Image_ 对象，并按照需求进行显示。

![basic_design_patterns_1](/img/in-post/basic_design_patterns/basic_design_patterns_1.jpg)

### 步骤 1
创建一个接口。

_Image.java_


```
public interface Image {
   void display();
}
```

### 步骤 2

创建实现接口的实体类。

_RealImage.java_


```
public class RealImage implements Image {

   private String fileName;

   public RealImage(String fileName){
      this.fileName = fileName;
      loadFromDisk(fileName);
   }

   @Override
   public void display() {
      System.out.println("Displaying " + fileName);
   }

   private void loadFromDisk(String fileName){
      System.out.println("Loading " + fileName);
   }
}
```
_ProxyImage.java_


```
public class ProxyImage implements Image{

   private RealImage realImage;
   private String fileName;

   public ProxyImage(String fileName){
      this.fileName = fileName;
   }

   @Override
   public void display() {
      if(realImage == null){
         realImage = new RealImage(fileName);
      }
      realImage.display();
   }
}
```

### 步骤 3

当被请求时，使用 _ProxyImage_ 来获取 _RealImage_ 类的对象。

_ProxyPatternDemo.java_


```
public class ProxyPatternDemo {
    
   public static void main(String[] args) {
      Image image = new ProxyImage("test_10mb.jpg");

      //图像将从磁盘加载
      image.display(); 
      System.out.println("");
      //图像将无法从磁盘加载
      image.display();     
   }
}
```

### 步骤 4

验证输出。


```
Loading test_10mb.jpg
Displaying test_10mb.jpg

Displaying test_10mb.jpg
```

本文摘要: [http://www.runoob.com/design-pattern/proxy-pattern.html](http://www.runoob.com/design-pattern/proxy-pattern.html)