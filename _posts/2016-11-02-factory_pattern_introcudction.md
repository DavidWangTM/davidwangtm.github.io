---
layout:     post
title:      "常用设计模式(二)"
subtitle:   "Factory 工厂模式"
date:       2016-11-03 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - 设计模式
--- 

# 工厂模式

工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

在工厂模式中，我们在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

## 介绍

- **意图：** 定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行。

- **主要解决：** 主要解决接口选择的问题。

- **何时使用：** 我们明确地计划不同条件下创建不同实例时。

- **如何解决：** 让其子类实现工厂接口，返回的也是一个抽象的产品。

- **关键代码：** 创建过程在其子类执行。

- **应用实例：**  1、您需要一辆汽车，可以直接从工厂里面提货，而不用去管这辆汽车是怎么做出来的，以及这个汽车里面的具体实现。 2、Hibernate 换数据库只需换方言和驱动就可以。

- **优点：**  1、一个调用者想创建一个对象，只要知道其名称就可以了。 2、扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。 3、屏蔽产品的具体实现，调用者只关心产品的接口。

- **缺点：** 每次增加一个产品时，都需要增加一个具体类和对象实现工厂，使得系统中类的个数成倍增加，在一定程度上增加了系统的复杂度，同时也增加了系统具体类的依赖。这并不是什么好事。

- **使用场景：** 1、日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方。 2、数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时。 3、设计一个连接服务器的框架，需要三个协议，"POP3"、"IMAP"、"HTTP"，可以把这三个作为产品类，共同实现一个接口。

- **注意事项：** 作为一种创建类模式，在任何需要生成复杂对象的地方，都可以使用工厂方法模式。有一点需要注意的地方就是复杂对象适合使用工厂模式，而简单对象，特别是只需要通过 new 就可以完成创建的对象，无需使用工厂模式。如果使用工厂模式，就需要引入一个工厂类，会增加系统的复杂度。

## 实现

我们将创建一个 _Shape_ 接口和实现 _Shape_ 接口的实体类。下一步是定义工厂类 _ShapeFactory_。

_FactoryPatternDemo_，我们的演示类使用 _ShapeFactory_ 来获取 _Shape_ 对象。它将向 _ShapeFactory_ 传递信息（_CIRCLE / RECTANGLE / SQUARE_），以便获取它所需对象的类型。

![basic_design_patterns_2](/img/in-post/basic_design_patterns/basic_design_patterns_2.jpg)

### 步骤 1

创建一个接口。

_Shape.java_

```
public interface Shape {
   void draw();
}
```

### 步骤 2

创建实现接口的实体类。

_Rectangle.java_

```
public class Rectangle implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Rectangle::draw() method.");
   }
}
```
_Square.java_

```
public class Square implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Square::draw() method.");
   }
}
```
_Circle.java_

```
public class Circle implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Circle::draw() method.");
   }
}
```

### 步骤 3

创建一个工厂，生成基于给定信息的实体类的对象。

_ShapeFactory.java_

```
public class ShapeFactory {
    
   //使用 getShape 方法获取形状类型的对象
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
}
```

### 步骤 4

使用该工厂，通过传递类型信息来获取实体类的对象。

_FactoryPatternDemo.java_

```
public class FactoryPatternDemo {

   public static void main(String[] args) {
      ShapeFactory shapeFactory = new ShapeFactory();

      //获取 Circle 的对象，并调用它的 draw 方法
      Shape shape1 = shapeFactory.getShape("CIRCLE");

      //调用 Circle 的 draw 方法
      shape1.draw();

      //获取 Rectangle 的对象，并调用它的 draw 方法
      Shape shape2 = shapeFactory.getShape("RECTANGLE");

      //调用 Rectangle 的 draw 方法
      shape2.draw();

      //获取 Square 的对象，并调用它的 draw 方法
      Shape shape3 = shapeFactory.getShape("SQUARE");

      //调用 Square 的 draw 方法
      shape3.draw();
   }
}
```

### 步骤 5

验证输出。

```
Inside Circle::draw() method.
Inside Rectangle::draw() method.
Inside Square::draw() method.
```

## 笔记部分

使用反射机制可以解决每次增加一个产品时，都需要增加一个对象实现工厂的缺点

```

public class ShapeFactory {
    public static Object getClass(Class<?extends Shape> clazz) {
        Object obj = null;

        try {
            obj = Class.forName(clazz.getName()).newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

        return obj;
    }
}
```

使用的使用采用强制转换

```
Rectangle rect = (Rectangle) ShapeFactory.getClass(Rectangle.class);
rect.draw();
Square square = (Square) ShapeFactory.getClass(Square.class);
square.draw();
```
这样就只需要一个对象实现工厂

```
public class ShapeFactory {
    public static <T> T getClass(Class<? extends T> clazz) {
        T obj = null;

        try {
            obj = (T) Class.forName(clazz.getName()).newInstance();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }

        return obj;
    }
}
```

省略类型强制转换，支持多态

```
Rectangle rect = ShapeFactory.getClass(Rectangle.class);
rect.draw();

Shape square = ShapeFactory.getClass(Square.class);
square.draw();
```

# 抽象工厂模式

抽象工厂模式（Abstract Factory Pattern）是围绕一个超级工厂创建其他工厂。该超级工厂又称为其他工厂的工厂。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

在抽象工厂模式中，接口是负责创建一个相关对象的工厂，不需要显式指定它们的类。每个生成的工厂都能按照工厂模式提供对象。

## 介绍

**意图：** 提供一个创建一系列相关或相互依赖对象的接口，而无需指定它们具体的类。

**主要解决：** 主要解决接口选择的问题。

**何时使用：** 系统的产品有多于一个的产品族，而系统只消费其中某一族的产品。

**如何解决：** 在一个产品族里面，定义多个产品。

**关键代码：** 在一个工厂里聚合多个同类产品。

**应用实例：** 工作了，为了参加一些聚会，肯定有两套或多套衣服吧，比如说有商务装（成套，一系列具体产品）、时尚装（成套，一系列具体产品），甚至对于一个家庭来说，可能有商务女装、商务男装、时尚女装、时尚男装，这些也都是成套的，即一系列具体产品。假设一种情况（现实中是不存在的，要不然，没法进入共产主义了，但有利于说明抽象工厂模式），在您的家中，某一个衣柜（具体工厂）只能存放某一种这样的衣服（成套，一系列具体产品），每次拿这种成套的衣服时也自然要从这个衣柜中取出了。用 OO 的思想去理解，所有的衣柜（具体工厂）都是衣柜类的（抽象工厂）某一个，而每一件成套的衣服又包括具体的上衣（某一具体产品），裤子（某一具体产品），这些具体的上衣其实也都是上衣（抽象产品），具体的裤子也都是裤子（另一个抽象产品）。

**优点：** 当一个产品族中的多个对象被设计成一起工作时，它能保证客户端始终只使用同一个产品族中的对象。

**缺点：** 产品族扩展非常困难，要增加一个系列的某一产品，既要在抽象的 Creator 里加代码，又要在具体的里面加代码。

**使用场景：**  1、QQ 换皮肤，一整套一起换。 2、生成不同操作系统的程序。

**注意事项：** 产品族难扩展，产品等级易扩展。

## 实现

我们将创建 _Shape_ 和 _Color_ 接口和实现这些接口的实体类。下一步是创建抽象工厂类 _AbstractFactory_。接着定义工厂类 _ShapeFactory_ 和 _ColorFactory_，这两个工厂类都是扩展了 _AbstractFactory_。然后创建一个工厂创造器/生成器类 _FactoryProducer_。

_AbstractFactoryPatternDemo_，我们的演示类使用 _FactoryProducer_ 来获取 _AbstractFactory_ 对象。它将向 _AbstractFactory_ 传递形状信息 _Shape_（_CIRCLE / RECTANGLE / SQUARE_），以便获取它所需对象的类型。同时它还向 _AbstractFactory_ 传递颜色信息 _Color_（_RED / GREEN / BLUE_），以便获取它所需对象的类型。

![basic_design_patterns_3](/img/in-post/basic_design_patterns/basic_design_patterns_3.jpg)

### 步骤 1

为形状创建一个接口。

_Shape.java_

```
public interface Shape {
   void draw();
}
```

### 步骤 2

创建实现接口的实体类。

_Rectangle.java_

```
public class Rectangle implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Rectangle::draw() method.");
   }
}
```
_Square.java_

```
public class Square implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Square::draw() method.");
   }
}
```
_Circle.java_

```
public class Circle implements Shape {

   @Override
   public void draw() {
      System.out.println("Inside Circle::draw() method.");
   }
}
```

### 步骤 3

为颜色创建一个接口。

_Color.java_

```
public interface Color {
   void fill();
}
```

### 步骤4

创建实现接口的实体类。

_Red.java_

```
public class Red implements Color {

   @Override
   public void fill() {
      System.out.println("Inside Red::fill() method.");
   }
}
```
_Green.java_

```
public class Green implements Color {

   @Override
   public void fill() {
      System.out.println("Inside Green::fill() method.");
   }
}
```
_Blue.java_

```
public class Blue implements Color {

   @Override
   public void fill() {
      System.out.println("Inside Blue::fill() method.");
   }
}
```

### 步骤 5

为 Color 和 Shape 对象创建抽象类来获取工厂。

_AbstractFactory.java_

```
public abstract class AbstractFactory {
   abstract Color getColor(String color);
   abstract Shape getShape(String shape) ;
}
```

### 步骤 6

创建扩展了 AbstractFactory 的工厂类，基于给定的信息生成实体类的对象。

_ShapeFactory.java_

```
public class ShapeFactory extends AbstractFactory {
    
   @Override
   public Shape getShape(String shapeType){
      if(shapeType == null){
         return null;
      }        
      if(shapeType.equalsIgnoreCase("CIRCLE")){
         return new Circle();
      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
         return new Rectangle();
      } else if(shapeType.equalsIgnoreCase("SQUARE")){
         return new Square();
      }
      return null;
   }
   
   @Override
   Color getColor(String color) {
      return null;
   }
}
```
_ColorFactory.java_

```
public class ColorFactory extends AbstractFactory {
    
   @Override
   public Shape getShape(String shapeType){
      return null;
   }
   
   @Override
   Color getColor(String color) {
      if(color == null){
         return null;
      }        
      if(color.equalsIgnoreCase("RED")){
         return new Red();
      } else if(color.equalsIgnoreCase("GREEN")){
         return new Green();
      } else if(color.equalsIgnoreCase("BLUE")){
         return new Blue();
      }
      return null;
   }
}
```

### 步骤 7

创建一个工厂创造器/生成器类，通过传递形状或颜色信息来获取工厂。

_FactoryProducer.java_

```
public class FactoryProducer {
   public static AbstractFactory getFactory(String choice){
      if(choice.equalsIgnoreCase("SHAPE")){
         return new ShapeFactory();
      } else if(choice.equalsIgnoreCase("COLOR")){
         return new ColorFactory();
      }
      return null;
   }
}
```

### 步骤 8

使用 FactoryProducer 来获取 AbstractFactory，通过传递类型信息来获取实体类的对象。

_AbstractFactoryPatternDemo.java_

```
public class AbstractFactoryPatternDemo {
   public static void main(String[] args) {

      //获取形状工厂
      AbstractFactory shapeFactory = FactoryProducer.getFactory("SHAPE");

      //获取形状为 Circle 的对象
      Shape shape1 = shapeFactory.getShape("CIRCLE");

      //调用 Circle 的 draw 方法
      shape1.draw();

      //获取形状为 Rectangle 的对象
      Shape shape2 = shapeFactory.getShape("RECTANGLE");

      //调用 Rectangle 的 draw 方法
      shape2.draw();
      
      //获取形状为 Square 的对象
      Shape shape3 = shapeFactory.getShape("SQUARE");

      //调用 Square 的 draw 方法
      shape3.draw();

      //获取颜色工厂
      AbstractFactory colorFactory = FactoryProducer.getFactory("COLOR");

      //获取颜色为 Red 的对象
      Color color1 = colorFactory.getColor("RED");

      //调用 Red 的 fill 方法
      color1.fill();

      //获取颜色为 Green 的对象
      Color color2 = colorFactory.getColor("Green");

      //调用 Green 的 fill 方法
      color2.fill();

      //获取颜色为 Blue 的对象
      Color color3 = colorFactory.getColor("BLUE");

      //调用 Blue 的 fill 方法
      color3.fill();
   }
}
```

### 步骤 9

验证输出。

```
Inside Circle::draw() method.
Inside Rectangle::draw() method.
Inside Square::draw() method.
Inside Red::fill() method.
Inside Green::fill() method.
Inside Blue::fill() method.
```
本文摘要: [http://www.runoob.com/design-pattern/factory-pattern.html](http://www.runoob.com/design-pattern/factory-pattern.html)
本文摘要: [http://www.runoob.com/design-pattern/abstract-factory-pattern.html](http://www.runoob.com/design-pattern/abstract-factory-pattern.html)