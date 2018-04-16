---
layout:     post
title:      "常用设计模式(四)"
subtitle:   "Delegate 委派模式"
date:       2016-11-04 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - 设计模式
--- 

# 委托模式

**委托模式** 是软件设计模式中的一项基本技巧。在委托模式中，有两个对象参与处理同一个请求，接受请求的对象将请求委托给另一个对象来处理。委托模式是一项基本技巧，许多其他的模式，如状态模式、策略模式、访问者模式本质上是在更特殊的场合采用了委托模式。委托模式使得我们可以用聚合"聚合 (计算机)（页面不存在）")来替代继承"继承 (计算机科学)")，它还使我们可以模拟mixin。

### 简单的Java例子

在这个例子里，类模拟打印机Printer拥有针式打印机RealPrinter的实例，Printer拥有的方法print()将处理转交给RealPrinter的方法print()。


```
class RealPrinter { // the "delegate"
     void print() { 
       System.out.print("something"); 
     }
 }
 
 class Printer { // the "delegator"
     RealPrinter p = new RealPrinter(); // create the delegate 
     void print() { 
       p.print(); // delegation
     } 
 }
 
 public class Main {
     // to the outside world it looks like Printer actually prints.
     public static void main(String[] args) {
         Printer printer = new Printer();
         printer.print();
     }
 }
```

### 复杂的Java例子

通过使用接口，委托可以做到类型安全并且更加灵活。在这个例子里，类别C可以委托类别A或类别B，类别C拥有方法使自己可以在类别A或类别B间选择。因为类别A或类别B必须实现接口I规定的方法，所以在这里委托是类型安全的。这个例子显示出委托的缺点是需要更多的代码。


```
 interface I {
     void f();
     void g();
 }
 
 class A implements I {
     public void f() { System.out.println("A: doing f()"); }
     public void g() { System.out.println("A: doing g()"); }
 }
 
 class B implements I {
     public void f() { System.out.println("B: doing f()"); }
     public void g() { System.out.println("B: doing g()"); }
 }
 
 class C implements I {
     // delegation
     I i = new A();
 
     public void f() { i.f(); }
     public void g() { i.g(); }
 
     // normal attributes
     public void toA() { i = new A(); }
     public void toB() { i = new B(); }
 }
 
 
 public class Main {
     public static void main(String[] args) {
         C c = new C();
         c.f();     // output: A: doing f()
         c.g();     // output: A: doing g()
         c.toB();
         c.f();     // output: B: doing f()
         c.g();     // output: B: doing g()
     }
 }
```

本文摘要:[https://zh.wikipedia.org/wiki/%E5%A7%94%E6%89%98%E6%A8%A1%E5%BC%8F](https://zh.wikipedia.org/wiki/%25E5%25A7%2594%25E6%2589%2598%25E6%25A8%25A1%25E5%25BC%258F)