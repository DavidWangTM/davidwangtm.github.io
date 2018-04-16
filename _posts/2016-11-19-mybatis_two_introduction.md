---
layout:     post
title:      "Mybatis回顾 二"
subtitle:   "Mybatis回顾-Spring 集成下的SqlSession 和 Mapper 以及 Mybatis的事务"
date:       2016-11-19 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Mybatis
--- 

# Mybatis回顾

## Spring 集成下的 SqlSession

在 MyBatis 中,你可以使用 SqlSessionFactory 来创建 SqlSession。一旦你获得一个 session 之后,你可以使用它来执行映射语句,提交或回滚连接,最后,当不再需要它的时 候, 你可以关闭 session。 使用 MyBatis-Spring 之后, 你不再需要直接使用 SqlSessionFactory 了,因为你的 bean 可以通过一个线程安全的 SqlSession 来注入,基于 Spring 的事务配置 来自动提交,回滚,关闭 session。

注意通常不必直接使用 SqlSession。 在大多数情况下 MapperFactoryBean, 将会在 bean 中注入所需要的映射器。

### SqlSessionTemplate

SqlSessionTemplate 是 MyBatis-Spring 的核心。 这个类负责管理 MyBatis 的 SqlSession, 调用 MyBatis 的 SQL 方法, 翻译异常。 SqlSessionTemplate 是线程安全的, 可以被多个 DAO 所共享使用。

当调用 SQL 方法时, 包含从映射器 getMapper()方法返回的方法, SqlSessionTemplate 将会保证使用的 SqlSession 是和当前 Spring 的事务相关的。此外,它管理 session 的生命 周期,包含必要的关闭,提交或回滚操作。

SqlSessionTemplate 实现了 SqlSession 接口,这就是说,在代码中无需对 MyBatis 的 SqlSession 进行替换。 SqlSessionTemplate 通常是被用来替代默认的 MyBatis 实现的 DefaultSqlSession , 因为模板可以参与到 Spring 的事务中并且被多个注入的映射器类所使 用时也是线程安全的。相同应用程序中两个类之间的转换可能会引起数据一致性的问题。

SqlSessionTemplate 对象可以使用 SqlSessionFactory 作为构造方法的参数来创建。

```
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
  <constructor-arg index="0" ref="sqlSessionFactory" />
</bean>
```

这个 bean 现在可以直接注入到 DAO bean 中。你需要在 bean 中添加一个 SqlSession 属性,就像下面的代码:

```
public class UserDaoImpl implements UserDao {

  private SqlSession sqlSession;

  public void setSqlSession(SqlSession sqlSession) {
    this.sqlSession = sqlSession;
  }

  public User getUser(String userId) {
    return (User) sqlSession.selectOne("org.mybatis.spring.sample.mapper.UserMapper.getUser", userId);
  }
}
```

如下注入 SqlSessionTemplate:

```
<bean id="userDao" class="org.mybatis.spring.sample.dao.UserDaoImpl">
  <property name="sqlSession" ref="sqlSession" />
</bean>
```

SqlSessionTemplate 有一个使用 ExecutorType 作为参数的构造方法。这允许你用来 创建对象,比如,一个批量 SqlSession,但是使用了下列 Spring 配置的 XML 文件:

```
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
  <constructor-arg index="0" ref="sqlSessionFactory" />
  <constructor-arg index="1" value="BATCH" />
</bean>
```

现在你所有的语句可以批量操作了,下面的语句就可以在 DAO 中使用了

```
public void insertUsers(User[] users) {
   for (User user : users) {
     sqlSession.insert("org.mybatis.spring.sample.mapper.UserMapper.insertUser", user);
   }
 }
```

### SqlSessionDaoSupport

SqlSessionDaoSupport 是一个抽象的支持类, 用来为你提供SqlSession 。 调用getSqlSession()方法你会得到一个 SqlSessionTemplate,之后可以用于执行 SQL 方法, 就像下面这样:

```
public class UserDaoImpl extends SqlSessionDaoSupport implements UserDao {
  public User getUser(String userId) {
    return (User) getSqlSession().selectOne("org.mybatis.spring.sample.mapper.UserMapper.getUser", userId);
  }
}
```

通常 MapperFactoryBean 是这个类的首选,因为它不需要额外的代码。但是,如果你 需要在 DAO 中做其它非 MyBatis 的工作或需要具体的类,那么这个类就很有用了。

SqlSessionDaoSupport 需要一个 sqlSessionFactory 或 sqlSessionTemplate 属性来 设 置 。 这 些 被 明 确 地 设 置 或 由 Spring 来 自 动 装 配 。 如 果 两 者 都 被 设 置 了 , 那 么 SqlSessionFactory 是被忽略的。

假设类 UserMapperImpl 是 SqlSessionDaoSupport 的子类,它可以在 Spring 中进行如 下的配置:

```
<bean id="userMapper" class="org.mybatis.spring.sample.mapper.UserDaoImpl">
  <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```

## Spring 集成下的 Mapper

为了代替手工使用 SqlSessionDaoSupport 或 SqlSessionTemplate 编写数据访问对象 (DAO)的代码,MyBatis-Spring 提供了一个动态代理的实现:MapperFactoryBean。这个类 可以让你直接注入数据映射器接口到你的 service 层 bean 中。当使用映射器时,你仅仅如调 用你的 DAO 一样调用它们就可以了,但是你不需要编写任何 DAO 实现的代码,因为 MyBatis-Spring 将会为你创建代理。

使用注入的映射器代码,在 MyBatis,Spring 或 MyBatis-Spring 上面不会有直接的依赖。 MapperFactoryBean 创建的代理控制开放和关闭 session,翻译任意的异常到 Spring 的 DataAccessException 异常中。此外,如果需要或参与到一个已经存在活动事务中,代理将 会开启一个新的 Spring 事务。

### MapperFactoryBean

数据映射器接口可以按照如下做法加入到 Spring 中:

```
<bean id="userMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
  <property name="mapperInterface" value="org.mybatis.spring.sample.mapper.UserMapper" />
  <property name="sqlSessionFactory" ref="sqlSessionFactory" />
</bean>
```

MapperFactoryBean 创建的代理类实现了 UserMapper 接口,并且注入到应用程序中。 因为代理创建在运行时环境中(Runtime,译者注) ,那么指定的映射器必须是一个接口,而 不是一个具体的实现类。

如果 UserMapper 有一个对应的 MyBatis 的 XML 映射器文件, 如果 XML 文件在类路径的 位置和映射器类相同时, 它会被 MapperFactoryBean 自动解析。 没有必要在 MyBatis 配置文 件 中 去 指 定 映 射 器 , 除 非 映 射 器 的 XML 文 件 在 不 同 的 类 路 径 下 。

注意,当 MapperFactoryBean 需要 SqlSessionFactory 或 SqlSessionTemplate 时。 这些可以通过各自的 SqlSessionFactory 或 SqlSessionTemplate 属性来设置, 或者可以由 Spring 来自动装配。如果两个属性都设置了,那么 SqlSessionFactory 就会被忽略,因为 SqlSessionTemplate 是需要有一个 session 工厂的设置; 那个工厂会由 MapperFactoryBean. 来使用。

你可以直接在 business/service 对象中以和注入任意 Spring bean 的相同方式直接注入映 射器:

```
<bean id="fooService" class="org.mybatis.spring.sample.mapper.FooServiceImpl">
  <property name="userMapper" ref="userMapper" />
</bean>
```

这个 bean 可以直接在应用程序逻辑中使用:

```
public class FooServiceImpl implements FooService {

  private UserMapper userMapper;

  public void setUserMapper(UserMapper userMapper) {
    this.userMapper = userMapper;
  }

  public User doSomeBusinessStuff(String userId) {
    return this.userMapper.getUser(userId);
  }
}
```

注意在这段代码中没有 SqlSession 或 MyBatis 的引用。也没有任何需要创建,打开或 关闭 session 的代码,MyBatis-Spring 会来关心它的。

### MapperScannerConfigurer

有必要在 Spring 的 XML 配置文件中注册所有的映射器。相反,你可以使用一个 MapperScannerConfigurer , 它 将 会 查 找 类 路 径 下 的 映 射 器 并 自 动 将 它 们 创 建 成 MapperFactoryBean。

要创建 MapperScannerConfigurer,可以在 Spring 的配置中添加如下代码:

```
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
  <property name="basePackage" value="org.mybatis.spring.sample.mapper" />
</bean>
```

basePackage 属性是让你为映射器接口文件设置基本的包路径。 你可以使用分号或逗号 作为分隔符设置多于一个的包路径。每个映射器将会在指定的包路径中递归地被搜索到。

MapperScannerConfigurer 属性不支持使用了 PropertyPlaceholderConfigurer 的属 性替换,因为会在 Spring 其中之前来它加载。但是,你可以使用 PropertiesFactoryBean 和 SpEL 表达式来作为替代。

注 意 , 没 有 必 要 去 指 定 SqlSessionFactory 或 SqlSessionTemplate , 因 为 MapperScannerConfigurer 将会创建 MapperFactoryBean,之后自动装配。但是,如果你使 用了一个 以上的 DataSource ,那 么自动 装配可 能会失效 。这种 情况下 ,你可 以使用 sqlSessionFactoryBeanName 或 sqlSessionTemplateBeanName 属性来设置正确的 bean 名 称来使用。这就是它如何来配置的,注意 bean 的名称是必须的,而不是 bean 的引用,因 此,value 属性在这里替代通常的 ref:

```
<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
```

MapperScannerConfigurer 支 持 过 滤 由 指 定 的 创 建 接 口 或 注 解 创 建 映 射 器 。 annotationClass 属性指定了要寻找的注解名称。 markerInterface 属性指定了要寻找的父 接口。如果两者都被指定了,加入到接口中的映射器会匹配两种标准。默认情况下,这两个 属性都是 null,所以在基包中给定的所有接口可以作为映射器加载。

被发现的映射器将会使用 Spring 对自动侦测组件(参考 Spring 手册的 3.14.4)默认的命 名策略来命名。也就是说,如果没有发现注解,它就会使用映射器的非大写的非完全限定类 名。但是如果发现了@Component 或 JSR-330 的@Named 注解,它会获取名称。注意你可以 配 置 到 org.springframework.stereotype.Component , javax.inject.Named(如果你使用 JSE 6 的话)或你自己的注解(肯定是自我注解)中,这 样注解将会用作生成器和名称提供器。


## Mybatis的事务

一个使用 MyBatis-Spring 的主要原因是它允许 MyBatis 参与到 Spring 的事务管理中。而 不是给 MyBatis 创建一个新的特定的事务管理器,MyBatis-Spring 利用了存在于 Spring 中的 DataSourceTransactionManager。

一旦 Spring 的 PlatformTransactionManager 配置好了,你可以在 Spring 中以你通常的做 法来配置事务。@Transactional 注解和 AOP(Aspect-Oriented Program,面向切面编程,译 者注)样式的配置都是支持的。在事务处理期间,一个单独的 SqlSession 对象将会被创建 和使用。当事务完成时,这个 session 会以合适的方式提交或回滚。

一旦事务创建之后,MyBatis-Spring 将会透明的管理事务。在你的 DAO 类中就不需要额 外的代码了

### 标准配置

要 开 启 Spring 的 事 务 处 理 , 在 Spring 的 XML 配 置 文 件 中 简 单 创 建 一 个 DataSourceTransactionManager 对象:

```
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  <property name="dataSource" ref="dataSource" />
</bean>
```

指定的 DataSource 一般可以是你使用 Spring 的任意 JDBC DataSource。这包含了连接 池和通过 JNDI 查找获得的 DataSource。

要注意, 为事务管理器指定的 DataSource 必须和用来创建 SqlSessionFactoryBean 的 是同一个数据源,否则事务管理器就无法工作了。

### 容器管理事务

如果你正使用一个 JEE 容器而且想让 Spring 参与到容器管理事务(Container managed transactions,CMT,译者注)中,那么 Spring 应该使用 JtaTransactionManager 或它的容 器指定的子类来配置。做这件事情的最方便的方式是用 Spring 的事务命名空间:

```<tx:jta-transaction-manager />```

在这种配置中,MyBatis 将会和其它由 CMT 配置的 Spring 事务资源一样。Spring 会自动 使用任意存在的容器事务,在上面附加一个 SqlSession。如果没有开始事务,或者需要基 于事务配置,Spring 会开启一个新的容器管理事务。

注 意 , 如 果 你 想 使 用 CMT , 而 不 想 使 用 Spring 的 事 务 管 理 , 你 就 必 须 配 置 SqlSessionFactoryBean 来使用基本的 MyBatis 的 ManagedTransactionFactory 而不是其 它任意的 Spring 事务管理器:

```
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
  <property name="dataSource" ref="dataSource" />
  <property name="transactionFactory">
    <bean class="org.apache.ibatis.transaction.managed.ManagedTransactionFactory" />
  </property>  
</bean>
```

### 编程式事务管理

MyBatis 的 SqlSession 提供指定的方法来处理编程式的事务。 但是当使用 MyBatis-Spring 时, bean 将会使用 Spring 管理的 SqlSession 或映射器来注入。 那就是说 Spring 通常是处理 事务的。

你 不 能 在 Spring 管 理 的 SqlSession 上 调 用 SqlSession.commit() , SqlSession.rollback() 或 SqlSession.close() 方 法 。 如 果 这 样 做 了 , 就 会 抛 出 UnsupportedOperationException 异常。注意在使用注入的映射器时不能访问那些方法。

无论 JDBC 连接是否设置为自动提交, SqlSession 数据方法的执行或在 Spring 事务之外 任意调用映射器方法都将会自动被提交。

如果你想编程式地控制事务,请参考 Spring 手册的 10.6 节。这段代码展示了如何手动 使用在 10.6.2 章节描述的 PlatformTransactionManager 来处理事务。

```
DefaultTransactionDefinition def = new DefaultTransactionDefinition();
def.setPropagationBehavior(TransactionDefinition.PROPAGATION_REQUIRED);

TransactionStatus status = txManager.getTransaction(def);
try {
  userMapper.insertUser(user);
}
catch (MyException ex) {
  txManager.rollback(status);
  throw ex;
}
txManager.commit(status);
```

注意这段代码展示了一个映射器,但它也能和 SqlSession 一起使用。


### 注解事务

在基础事务上配置 注解事务 @Transactional

```<tx:annotation-driven transaction-manager="transactionManager"/>```



 

