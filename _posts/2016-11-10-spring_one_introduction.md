---
layout:     post
title:      "Spring回顾 (一)"
subtitle:   "IOC 容器设计原理及高级特性"
date:       2016-11-10 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Spring
    - IOC
--- 

IOC容器设计原理及高级特性
===

## 一、IOC接口设计

IOC容器设计的源码主要在spring-beans.jar、spring-context.jar这两个包中。IOC容器主要接口设计如下：

![basic_design_patterns_8](/img/in-post/basic_design_patterns/basic_design_patterns_8.png)

这里的接口设计有两条主线：BeanFactory和ApplicationContext

1. `BeanFactory`-->`HierarchicalBeanFactory`-->`ConfigurableBeanFactory`:这是`BeanFactory`的设计路线，`BeanFactory`定义了基本的IOC容器规范，`HierarchicalBeanFactory`中增加了`getParentBeanFactory`方法，具备了双亲IOC容器的管理功能；`ConfigurableBeanFactory`中新增一些配置功能。

2. `ApplicationContext`应用上下文接口：继承了`HierarchicalBeanFactory`、`ListableBeanFactory`等`BeanFactory`的子接口，这条分支使得`ApplicationContext`具备了IOC容器的基本功能；在继承`MessageSource`、`ApplicationEventPublisher`等接口的时候，使得`ApplicationContext`这个简单的IOC容器添加了许多高级容器的特性。`ApplicationContext`的子接口有`ConfigurableApplicationContext`以及在WEB环境下使用的`WebApplicationContext`。

## 二、BeanFactory的设计原理

```
public interface BeanFactory {
    String FACTORY_BEAN_PREFIX = "&";
	//根据名字获取Bean    
	Object getBean(String var1) throws BeansException;    
	<T> T getBean(String var1, Class<T> var2) throws BeansException;    
	<T> T getBean(Class<T> var1) throws BeansException;    
	Object getBean(String var1, Object... var2) throws BeansException;    
	<T> T getBean(Class<T> var1, Object... var2) throws BeansException; 
	//判断是否含有Bean  
	boolean containsBean(String var1); 
	//Bean是否是单例  
	boolean isSingleton(String var1) throws NoSuchBeanDefinitionException;
	//Bean是否是原型	   						   	
 	boolean isPrototype(String var1) throws NoSuchBeanDefinitionException;
	//Bean是否是targetType对应类型   
	boolean isTypeMatch(String var1, ResolvableType var2) throws NoSuchBeanDefinitionException;   
	boolean isTypeMatch(String var1, Class<?> var2) throws NoSuchBeanDefinitionException;    
	Class<?> getType(String var1) throws NoSuchBeanDefinitionException;
	//获取Bean的所有别名    
	String[] getAliases(String var1); 
}
```
在`BeanFactory`里只对IOC容器的基本行为作了定义，根本不关心你的bean是如何定义怎样加载的。正如我们只关心工厂里得到什么的产品对象，至于工厂是怎么生产这些对象的，这个基本的接口不关心。

### 如何创建 BeanFactory 工厂

Ioc 容器实际上就是 Context 组件结合其他两个组件共同构建了一个 Bean 关系网，如何构建这个关系网？构建的入口就在 `AbstractApplicationContext` 类的 `refresh` 方法中。这个方法的代码如下：

_AbstractApplicationContext_ `refresh`

```
public void refresh() throws BeansException, IllegalStateException {
//创建synchronized所使用的对象监视器 var1
    Object var1 = this.startupShutdownMonitor;
 synchronized(this.startupShutdownMonitor) {
	//为刷新准备新的context
    this.prepareRefresh();
	//刷新所有BeanFactory子容器
    ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
    //创建BeanFactory
    this.prepareBeanFactory(beanFactory);   
	try {
		//注册实现了BeanPostProcessor接口的bean
       	this.postProcessBeanFactory(beanFactory);
		//初始化和执行BeanFactoryPostProcessor beans
 		this.invokeBeanFactoryPostProcessors(beanFactory);
		//初始化和执行BeanPostProcessors beans
 		this.registerBeanPostProcessors(beanFactory);
		//初始化MessageSource
 		this.initMessageSource();
		//初始化 event multicaster
 		this.initApplicationEventMulticaster();
		//刷新由子类实现的方法
 		this.onRefresh();
		//检查注册事件
 		this.registerListeners();
		//初始化non-lazy-init单利Bean
 		this.finishBeanFactoryInitialization(beanFactory);
		//执行LifecycleProcessor.onRefresh()和ContextRefreshedEvent事件
		this.finishRefresh();
  		} catch (BeansException var9) {
            if (this.logger.isWarnEnabled()) {
                this.logger.warn("Exception encountered during context initialization - cancelling refresh attempt: " + var9);
  }
		//销毁Beans
        this.destroyBeans();
 		this.cancelRefresh(var9);
 		throw var9;
  } finally {
            this.resetCommonCaches();
  }
    }
}
```
这个方法就是构建整个 Ioc 容器过程的完整的代码，了解了里面的每一行代码基本上就了解大部分 Spring 的原理和功能了。

这段代码主要包含这样几个步骤：

*   构建 BeanFactory，以便于产生所需的“演员”
*   注册可能感兴趣的事件
*   创建 Bean 实例对象
*   触发被监听的事件

下面就结合代码分析这几个过程。
```
ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory(); this.prepareBeanFactory(beanFactory);
```
第二三句就是在创建和配置 `BeanFactory`。这里是 refresh 也就是刷新配置，前面介绍了 Context 有可更新的子类，这里正是实现这个功能，当 `BeanFactory` 已存在是就更新，如果没有就新创建。下面是更新 `BeanFactory` 的方法代码：

_AbstractRefreshableApplicationContext_ `refreshBeanFactory`

```
protected final void refreshBeanFactory() throws BeansException {
    if (this.hasBeanFactory()) {
        this.destroyBeans();
 		this.closeBeanFactory();
  	}
	try {
		//BeanFactory 的原始对象是 DefaultListableBeanFactory
        DefaultListableBeanFactory beanFactory = this.createBeanFactory();
  		beanFactory.setSerializationId(this.getId());
 		this.customizeBeanFactory(beanFactory);	
		//这个方法将开始加载、解析 Bean 的定义，也就是把用户定义的数据结构转化为 Ioc 容器中的特定数据结构。
 		this.loadBeanDefinitions(beanFactory);
  		Object var2 = this.beanFactoryMonitor;
 		synchronized(this.beanFactoryMonitor) {
            this.beanFactory = beanFactory;
  		}
    } catch (IOException var5) {
        throw new ApplicationContextException("I/O error parsing bean definition source for " + this.getDisplayName(), var5);
  	}
}
```
这个方法实现了 `AbstractApplicationContext` 的抽象方法 `refreshBeanFactory`，这段代码清楚的说明了 `BeanFactory` 的创建过程。注意 `BeanFactory` 对象的类型的变化，前面介绍了他有很多子类，在什么情况下使用不同的子类这非常关键。`BeanFactory` 的原始对象是 `DefaultListableBeanFactory`，这个非常关键，因为他设计到后面对这个对象的多种操作，下面看一下这个类的继承层次类图：

![basic_design_patterns_9](/img/in-post/basic_design_patterns/basic_design_patterns_9.png)

从这个图中发现除了 `BeanFactory` 相关的类外，还发现了与 Bean 的 register 相关。这在 `refreshBeanFactory` 方法中有一行` loadBeanDefinitions(beanFactory)` 将找到答案，这个方法将开始加载、解析 Bean 的定义，也就是把用户定义的数据结构转化为 Ioc 容器中的特定数据结构。

这个过程可以用下面时序图解释：

![basic_design_patterns_10](/img/in-post/basic_design_patterns/basic_design_patterns_10.png)

Bean 的解析和登记流程时序图如下：

![basic_design_patterns_11](/img/in-post/basic_design_patterns/basic_design_patterns_11.png)

创建好 `BeanFactory` 后，接下去添加一些 Spring 本身需要的一些工具类，这个操作在 `AbstractApplicationContext` 的 `prepareBeanFactory` 方法完成。

```
AbstractApplicationContext 中接下来的三行代码对 Spring 的功能扩展性起了至关重要的作用。前两行主要是让你现在可以对已经构建的 BeanFactory 的配置做修改，后面一行就是让你可以对以后再创建 Bean 的实例对象时添加一些自定义的操作。所以他们都是扩展了 Spring 的功能，所以我们要学习使用 Spring 必须对这一部分搞清楚。
```
其中在 invokeBeanFactoryPostProcessors 方法中主要是获取实现 BeanFactoryPostProcessor 接口的子类。并执行它的 postProcessBeanFactory 方法。

这个方法的声明如下： 

```
BeanFactoryPostProcessor

void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;
```
它的参数是 beanFactory，说明可以对 beanFactory 做修改。

```
这里注意这个 beanFactory 是 ConfigurableListableBeanFactory 类型的，说明不同 BeanFactory 所使用的场合不同，这里只能是可配置的 BeanFactory，防止一些数据被用户随意修改。
```
`registerBeanPostProcessors` 方法也是可以获取用户定义的实现了 `BeanPostProcessor` 接口的子类，并执行把它们注册到 `BeanFactory` 对象中的 `beanPostProcessors` 变量中。

eanPostProcessor 中声明了两个方法：

*   `postProcessBeforeInitialization`、`postProcessAfterInitialization` 分别用于在 Bean 对象初始化时执行。可以执行用户自定义的操作。

后面的几行代码是初始化监听事件和对系统的其他监听者的注册，监听者必须是 ApplicationListener 的子类。

### 创建Bean实例并构建关系网

下面就是 Bean 的实例化代码，是从 finishBeanFactoryInitialization 方法开始的。

```
AbstractApplicationContext

protected  void  finishBeanFactoryInitialization( 
	ConfigurableListableBeanFactory beanFactory) { 
	// 不使用
	TempClassLoader beanFactory.setTempClassLoader(null); 
	// 禁止修改当前Bean的配置信息 
	beanFactory.freezeConfiguration(); 
	// 实例化non-lazy-init类型的bean 
	beanFactory.preInstantiateSingletons(); 
}

从上面代码中可以发现 Bean 的实例化是在 BeanFactory 中发生的。
```
`preInstantiateSingletons` 方法的代码如下：

```
    public void preInstantiateSingletons() throws BeansException {
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Pre-instantiating singletons in " + this);
        }

        List<String> beanNames = new ArrayList(this.beanDefinitionNames);
        Iterator var2 = beanNames.iterator();

        while(true) {
            while(true) {
                String beanName;
                RootBeanDefinition bd;
                do {
                    do {
                        do {
                            if (!var2.hasNext()) {
                                var2 = beanNames.iterator();

                                while(var2.hasNext()) {
                                    beanName = (String)var2.next();
                                    Object singletonInstance = this.getSingleton(beanName);
                                    if (singletonInstance instanceof SmartInitializingSingleton) {
                                        final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton)singletonInstance;
                                        if (System.getSecurityManager() != null) {
                                            AccessController.doPrivileged(new PrivilegedAction<Object>() {
                                                public Object run() {
                                                    smartSingleton.afterSingletonsInstantiated();
                                                    return null;
                                                }
                                            }, this.getAccessControlContext());
                                        } else {
                                            smartSingleton.afterSingletonsInstantiated();
                                        }
                                    }
                                }

                                return;
                            }

                            beanName = (String)var2.next();
                            bd = this.getMergedLocalBeanDefinition(beanName);
                        } while(bd.isAbstract());
                    } while(!bd.isSingleton());
                } while(bd.isLazyInit());

                if (this.isFactoryBean(beanName)) {
                    final FactoryBean<?> factory = (FactoryBean)this.getBean("&" + beanName);
                    boolean isEagerInit;
                    if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
                        isEagerInit = ((Boolean)AccessController.doPrivileged(new PrivilegedAction<Boolean>() {
                            public Boolean run() {
                                return ((SmartFactoryBean)factory).isEagerInit();
                            }
                        }, this.getAccessControlContext())).booleanValue();
                    } else {
                        isEagerInit = factory instanceof SmartFactoryBean && ((SmartFactoryBean)factory).isEagerInit();
                    }

                    if (isEagerInit) {
                        this.getBean(beanName);
                    }
                } else {
                    this.getBean(beanName);
                }
            }
        }
    }

```
**FactoryBean是个特殊的 Bean， 他是个工厂 Bean，可以产生 Bean 的 Bean**，这里的产生 Bean 是指 Bean 的实例。

```
如果一个类继承 FactoryBean 用户可以自己定义产生实例对象的方法只要实现他的 getObject 方法。然而在 Spring 内部这个 Bean 的实例对象是 FactoryBean，通过调用这个对象的 getObject 方法就能获取用户自定义产生的对象，从而为 Spring 提供了很好的扩展性。Spring 获取 FactoryBean 本身的对象是在前面加上 & 来完成的。
```
如何创建 Bean 的实例对象以及如何构建 Bean 实例对象之间的关联关系式 Spring 中的一个核心关键，下面是这个过程的流程图：

![basic_design_patterns_12](/img/in-post/basic_design_patterns/basic_design_patterns_12.gif)

如果是普通的 Bean 就直接创建他的实例，是通过调用 getBean 方法。下面是创建 Bean 实例的时序图：

![basic_design_patterns_13](/img/in-post/basic_design_patterns/basic_design_patterns_13.png)

还有一个非常重要的部分就是建立 Bean 对象实例之间的关系，这也是 Spring 框架的核心竞争力，何时、如何建立他们之间的关系请看下面的时序图：

![basic_design_patterns_14](/img/in-post/basic_design_patterns/basic_design_patterns_14.png)

###  扩展点

```
对 Spring 的 Ioc 容器来说，主要有BeanFactoryPostProcessor， BeanPostProcessor。它们分别是在构建 BeanFactory 和构建 Bean 对象时调用。还有就是 InitializingBean 和 DisposableBean 他们分别是在 Bean 实例创建和销毁时被调用。用户可以实现这些接口中定义的方法，Spring 就会在适当的时候调用他们。还有一个是 FactoryBean 他是个特殊的 Bean，这个 Bean 可以被用户更多的控制。 
这些扩展点通常也是我们使用 Spring 来完成我们特定任务的地方，如何精通 Spring 就看你有没有掌握好 Spring 有哪些扩展点，并且如何使用他们，要知道如何使用他们就必须了解他们内在的机理。可以用下面一个比喻来解释。
```
### 生动描述Ioc

```
我们把 Ioc 容器比作一个箱子，这个箱子里有若干个球的模子，可以用这些模子来造很多种不同的球，还有一个造这些球模的机器，这个机器可以产生球模。那么他们的对应关系就是 BeanFactory 就是那个造球模的机器，球模就是 Bean，而球模造出来的球就是 Bean 的实例。那前面所说的几个扩展点又在什么地方呢？ BeanFactoryPostProcessor 对应到当造球模被造出来时，你将有机会可以对其做出设当的修正，也就是他可以帮你修改球模。而 InitializingBean 和 DisposableBean 是在球模造球的开始和结束阶段，你可以完成一些预备和扫尾工作。BeanPostProcessor 就可以让你对球模造出来的球做出适当的修正。最后还有一个 FactoryBean，它可是一个神奇的球模。这个球模不是预先就定型了，而是由你来给他确定它的形状，既然你可以确定这个球模型的形状，当然他造出来的球肯定就是你想要的球了，这样在这个箱子里尼可以发现所有你想要的球
```
## IOC高级特性

### lazy-init延迟加载

设置了`lazy-init=false`属性（默认设置）后，IoC容器会进行Bean对象的预加载。

*   refresh()方法是IoC容器初始化开始的地方，在容器启动bean资源载入注册后，调用`finifshBeanFactoryInitialization()`方法准备进行bean的预实例化；
*   实际上，`DefaultListableBeanFactory`的`preInstantiateSingletons()`方法进行预实例化，这个Bean在单例模式下会有线程同步来保证数据一致性；
*   判断`lazy-init`的属性值，然后调用**`getBean()`**方法触发容器对bean实例化和依赖注入过程

### FactoryBean和BeanFactory

*   `BeanFactory`是bean工厂，Spring IoC最顶层的接口，作用是管理bean，即定位、配置对象、实例化和建立对象之间的依赖，`getObject()`方法由实现类调用，来创建bean实例化
*   `FactoryBean`工厂bean，作用是产生其它bean实例，提供工厂方法，返回其它bean实例，一般Spring容器担任工厂角色

### BeanPostFactory后置处理器

一个监听器，可以监听容器触发的bean声明周期事件；如果后置处理器向容器注册以后，容器管理的bean就具备了接收IoC容器时间回调的能力，可以为AOP的bean注册提供方便

### @Autowire注解实现

注解相当于一种声明，通过反射去取对应的值。一般使用该注解的对象都是我们自己定义的Bean对象，Spring在初始化这些对象时都是默认调用该类的无参构造方法。因此对于@Autowire注解的属性，Spring会解析出并调用相应的构造方法：

*   从构造方法缓存中查询其构造方法
*   若缓存不存在，则根据反射获取所有构造方法
*   遍历所有构造方法，查询器是否含有@Autowrie属性
*   判断Autowire注解中指定了required属性（判断是否强依赖），若存在required就使用默认构造方法
*   返回指定的构造方法

接下来是开始初始化，bean的自动装配发生在容器对bean的依赖注入过程中根据注解对属性注入

*   在第一次调用`getBean()`进行依赖注入时，完成依赖bean的初始化和注入
*   将依赖bean的属性引用设置到被依赖的bean属性上
*   将依赖bean的名称和被依赖bean的名称存储在IoC容器的集合中