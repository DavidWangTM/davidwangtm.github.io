---
layout:     post
title:      "Spring回顾 (二)"
subtitle:   "AOP 设计原理"
date:       2016-11-11 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Spring
--- 

## AOP的特点

AOP主要用于横切关注点分离和织入，因此需要理解横切关注点和织入：

- **关注点：** 可以认为是所关注的任何东西，比如上边的支付组件；

- **关注点分离：** 将问题细化从而单独部分，即可以理解为不可再分割的组件，如上边的日志组件和支付组件；

- **横切关注点：** 一个组件无法完成需要的功能，需要其他组件协作完成，如日志组件横切于支付组件；

- **织入：** 横切关注点分离后，需要通过某种技术将横切关注点融合到系统中从而完成需要的功能，因此需要织入，织入可能在编译期、加载期、运行期等进行。

横切关注点可能包含很多，比如非业务的：日志、事务处理、缓存、性能统计、权限控制等等这些非业务的基础功能；还可能是业务的：如某个业务组件横切于多个模块。如：

![basic_design_patterns_15](/img/in-post/basic_design_patterns/basic_design_patterns_15.png)

传统支付形式，流水方式：

![basic_design_patterns_16](/img/in-post/basic_design_patterns/basic_design_patterns_16.png)

面向切面方式，先将横切关注点分离，再将横切关注点织入到支付系统中：

![basic_design_patterns_17](/img/in-post/basic_design_patterns/basic_design_patterns_17.png)

## AOP的原理

### Spring内部创建代理对象的过程

在**Spring**的底层，如果我们配置了代理模式，**Spring**会为每一个**Bean**创建一个对应的**ProxyFactoryBean**的**FactoryBean**来创建某个对象的代理对象。

假定我们现在有一个接口**TicketService**及其实现类**RailwayStation**，我们打算创建一个代理类，在执行**TicketService**的方法时的各个阶段，插入对应的业务代码。

```
public interface TicketService {

    //售票
    public void sellTicket();

    //问询
    public void inquire();

    //退票
    public void withdraw();
}
```
```
public class RailwayStation implements TicketService {

    public void sellTicket(){
        System.out.println("售票............");
    }

    public void inquire() {
        System.out.println("问询.............");
    }

    public void withdraw() {
        System.out.println("退票.............");
    }
}
```
```
/**
 * 执行RealSubject对象的方法之前的处理意见
 */
public class TicketServiceBeforeAdvice implements MethodBeforeAdvice {

    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println("BEFORE_ADVICE: 欢迎光临代售点....");
    }
}

```
```
/**
 * 返回结果时后的处理意见
 */
public class TicketServiceAfterReturningAdvice implements AfterReturningAdvice {
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
        System.out.println("AFTER_RETURNING：本次服务已结束....");
    }
}
```
```
/**
 * 抛出异常时的处理意见
 */
public class TicketServiceThrowsAdvice implements ThrowsAdvice {

    public void afterThrowing(Exception ex){
        System.out.println("AFTER_THROWING....");
    }
    public void afterThrowing(Method method, Object[] args, Object target, Exception ex){
        System.out.println("调用过程出错啦！！！！！");
    }

} 
```
```
/**
 *
 * AroundAdvice
 */
public class TicketServiceAroundAdvice implements MethodInterceptor {
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        System.out.println("AROUND_ADVICE:BEGIN....");
        Object returnValue = invocation.proceed();
        System.out.println("AROUND_ADVICE:END.....");
        return returnValue;
    }
}
```
![basic_design_patterns_21](/img/in-post/basic_design_patterns/basic_design_patterns_21.png)

现在，我们来手动使用**ProxyFactoryBean**来创建**Proxy**对象，并将相应的几种不同的**Advice**加入这个**proxy**对应的各个执行阶段中：

```
/**
 * 通过ProxyFactoryBean 手动创建 代理对象
 * Created by louis on 2016/4/14.
 */
public class App {
    public static void main(String[] args) throws Exception {

        //1.针对不同的时期类型，提供不同的Advice
        Advice beforeAdvice = new TicketServiceBeforeAdvice();
        Advice afterReturningAdvice = new TicketServiceAfterReturningAdvice();
        Advice aroundAdvice = new TicketServiceAroundAdvice();
        Advice throwsAdvice = new TicketServiceThrowsAdvice();

        RailwayStation railwayStation = new RailwayStation();

        //2.创建ProxyFactoryBean,用以创建指定对象的Proxy对象
        ProxyFactoryBean proxyFactoryBean = new ProxyFactoryBean();
       //3.设置Proxy的接口
        proxyFactoryBean.setInterfaces(TicketService.class);
        //4. 设置RealSubject
        proxyFactoryBean.setTarget(railwayStation);
        //5.使用JDK基于接口实现机制的动态代理生成Proxy代理对象，如果想使用CGLIB，需要将这个flag设置成true
        proxyFactoryBean.setProxyTargetClass(true);

        //6. 添加不同的Advice

        proxyFactoryBean.addAdvice(afterReturningAdvice);
        proxyFactoryBean.addAdvice(aroundAdvice);
        proxyFactoryBean.addAdvice(throwsAdvice);
        proxyFactoryBean.addAdvice(beforeAdvice);
        proxyFactoryBean.setProxyTargetClass(false);
        //7通过ProxyFactoryBean生成Proxy对象
        TicketService ticketService = (TicketService) proxyFactoryBean.getObject();
        ticketService.sellTicket();
    }
}

输出：
AROUND_ADVICE:BEGIN....
BEFORE_ADVICE: 欢迎光临代售点....
售票............
AROUND_ADVICE:END.....
AFTER_RETURNING：本次服务已结束....
```
你会看到，我们成功地创建了一个通过一个**ProxyFactoryBean**和 真实的实例对象创建出了对应的代理对象，并将各个**Advice**加入到**proxy**代理对象中。

你会发现，在调用**RailwayStation**的**sellticket()**之前，成功插入了**BeforeAdivce**逻辑，而调用RailwayStation的**sellticket()**之后，AfterReturning逻辑也成功插入了。

AroundAdvice也成功包裹了sellTicket()方法，只不过这个AroundAdvice发生的时机有点让人感到迷惑。实际上，这个背后的执行逻辑隐藏了Spring AOP关于AOP的关于Advice调度最为核心的算法机制，这个将在本文后面详细阐述。 

另外，本例中**ProxyFactoryBean**是通过JDK的针对接口的动态代理模式生成代理对象的，具体机制，请看下面关于ProxyFactoryBean的介绍。

### Spring AOP的核心---ProxyFactoryBean

  上面我们通过了纯手动使用**ProxyFactoryBean**实现了AOP的功能。现在来分析一下上面的代码：我们为**ProxyFactoryBean**提供了如下信息：

- Proxy应该感兴趣的Adivce列表；
 
- 真正的实例对象引用ticketService;

- 告诉ProxyFactoryBean使用基于接口实现的JDK动态代理机制实现proxy: 

- Proxy应该具备的Interface接口：TicketService;

- 根据这些信息，**ProxyFactoryBean**就能给我们提供我们想要的Proxy对象了！那么，**ProxyFactoryBean**帮我们做了什么？

![basic_design_patterns_18](/img/in-post/basic_design_patterns/basic_design_patterns_18.png)

Spring 使用工厂Bean模式创建每一个Proxy，对应每一个不同的Class类型，在Spring中都会有一个相对应的**ProxyFactoryBean**. 以下是**ProxyFactoryBean**的类图。

![basic_design_patterns_19](/img/in-post/basic_design_patterns/basic_design_patterns_19.png)

如上所示，对于生成Proxy的工厂Bean而言，它要知道对其感兴趣的Advice信息，而这类的信息，被维护到Advised中。Advised可以根据特定的类名和方法名返回对应的AdviceChain，用以表示需要执行的Advice串。

### 基于JDK面向接口的动态代理JdkDynamicAopProxy生成代理对象

JdkDynamicAopProxy类实现了AopProxy，能够返回Proxy，并且，其自身也实现了InvocationHandler角色。也就是说，当我们使用proxy时，我们对proxy对象调用的方法，都最终被转到这个类的invoke()方法中。

### 各种Advice是的执行顺序是如何和方法调用进行结合的？

JdkDynamicAopProxy 和CglibAopProxy只是创建代理方式的两种方式而已，实际上我们为方法调用添加的各种Advice的执行逻辑都是统一的。在Spring的底层，会把我们定义的各个Adivce分别 包裹成一个 MethodInterceptor,这些Advice按照加入Advised顺序，构成一个AdivseChain.

![basic_design_patterns_22](/img/in-post/basic_design_patterns/basic_design_patterns_22.png)

各种Advice本质而言是一个方法调用拦截器，现在让我们看看各个Advice拦截器都干了什么？

![basic_design_patterns_23](/img/in-post/basic_design_patterns/basic_design_patterns_23.png)
![basic_design_patterns_24](/img/in-post/basic_design_patterns/basic_design_patterns_24.png)
![basic_design_patterns_25](/img/in-post/basic_design_patterns/basic_design_patterns_25.png)

关于AroundAdivce,其本身就是一个MethodInterceptor，所以不需要额外做转换了。

细心的你会发现，在拦截器串中，每个拦截器最后都会调用MethodInvocation的proceed()方法。如果按照简单的拦截器的执行串来执行的话，MethodInvocation的proceed()方法至少要执行N次(N表示拦截器Interceptor的个数)，因为每个拦截器都会调用一次proceed()方法。更直观地讲，比如我们调用了ticketService.sellTicket()方法，那么，按照这个逻辑，我们会打印出四条记录：

```
售票............  
售票............  
售票............  
售票............
```
_**这样我们肯定不是我们需要的结果！！！！**_因为按照我们的理解，只应该有一条"售票............"才对。真实的Spring的方法调用过程能够控制这个逻辑按照我们的思路执行，Spring将这个整个方法调用过程连同若干个Advice组成的拦截器链组合成ReflectiveMethodInvocation对象，让我们来看看这一执行逻辑是怎么控制的：

![basic_design_patterns_26](/img/in-post/basic_design_patterns/basic_design_patterns_26.png)

**实例分析**

根据上面的执行链上的逻辑，我们将我们上面举的例子的输出结果在整理一下：

Advice拦截器的添加顺序:

```
proxyFactoryBean.addAdvice(afterReturningAdvice);  
proxyFactoryBean.addAdvice(aroundAdvice);  
proxyFactoryBean.addAdvice(throwsAdvice);  
proxyFactoryBean.addAdvice(beforeAdvice);
```
还可以使用PointCut与Advice 结合 加入条件执行判断

```
AspectJExpressionPointcut pointcut = new AspectJExpressionPointcut();
pointcut.setExpression("execution( * sellTicket(..))");
//传入创建的beforeAdvice和pointcut  
FilteredAdvisor sellBeforeAdvior = new FilteredAdvisor(pointcut,beforeAdvice);  
//添加到FactoryBean中  
proxyFactoryBean.addAdvisor(sellBeforeAdvior);
```
本文摘要: [https://blog.csdn.net/luanlouis/article/details/51155821](https://blog.csdn.net/luanlouis/article/details/51155821)

