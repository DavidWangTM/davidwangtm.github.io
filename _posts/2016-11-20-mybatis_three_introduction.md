---
layout:     post
title:      "Mybatis回顾 三"
subtitle:   "Mybatis回顾-动态代理"
date:       2016-11-20 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Mybatis
--- 


# Mybatis回顾-动态代理

mybait有两种开发方式，一种是传统的dao开发方式，一种是Mapper动态代理方式。

传统为：配置XX.xml `<mapper resource="XX.xml"/>` 使用XX.xml中的 `<mapper namespace="test">` 的标记来找到结构内的方法.比如 `install`.
然后使用SqlSession `selectOne` `selectList` `insert（如果是事务需手动提交-commit）` 

动态代理： 配置 `<mapper resource="XXXMapper.xml包目录"/>` 需要创建 interface XXXMapper ，然后使用 SqlSession `getMapper(XXXMapper.class)` 获取Mapper对象，然后使用Mapper 来操作 XX.xml中的 `install （如果是事务需手动提交-commit）`

以上是使用Mybatis的动态代理。

## JDK动态代理

```
//接口类
public interface TestInterFace {
	public void showTest();
}
```

```
//实体类
public class TestMapperTarget implements TestInterFace{
	public void showTest() {
		System.out.println("测试内容");
	}
}
```

```
//处理类
public class TestHandler implements InvocationHandler {

	TestInterFace interFace;

	public TestHandler(TestInterFace interFace) {
		this.interFace = interFace;
	}

	public <T> T newInstance(Class<T> clz) {
		return (T) Proxy.newProxyInstance(clz.getClassLoader(), new Class[] { clz }, this);
	}

	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		after();
		interFace.showTest();
		before();
		return null;
	}

	public void before(){
		System.out.println("之后");
	}

	public void after(){
		System.out.println("之前");
	}
}
```

```
//测试类
public class TestMan {

	public static void main(String [] args) throws Exception{
		TestInterFace interFace = new TestMapperTarget();
		TestHandler handler = new TestHandler(interFace);
		TestInterFace testInterFace = handler.newInstance(TestInterFace.class);;
		System.out.println(testInterFace.getClass().getName());
		testInterFace.showTest();

		test(TestInterFace.class);
	}

	public static void test(Class c) throws Exception{
		byte[] data = ProxyGenerator.generateProxyClass("$Proxy0",new Class[]{c});
		FileOutputStream fileOutputStream = new FileOutputStream("$Proxy0.class");
		fileOutputStream.write(data);
		fileOutputStream.close();
	}

}

```

```
输出：
com.sun.proxy.$Proxy0
之前
测试内容
之后
```

```
//实现 TestInterFace 接口 ， 集成 Proxy
public final class $Proxy0 extends Proxy implements TestInterFace {
    private static Method m1;
    private static Method m2;
    private static Method m0;
    private static Method m3;

    public $Proxy0(InvocationHandler var1) throws  {
        super(var1);
    }

    public final boolean equals(Object var1) throws  {
        try {
            return ((Boolean)super.h.invoke(this, m1, new Object[]{var1})).booleanValue();
        } catch (RuntimeException | Error var3) {
            throw var3;
        } catch (Throwable var4) {
            throw new UndeclaredThrowableException(var4);
        }
    }

    public final String toString() throws  {
        try {
            return (String)super.h.invoke(this, m2, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final int hashCode() throws  {
        try {
            return ((Integer)super.h.invoke(this, m0, (Object[])null)).intValue();
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    public final void showTest() throws  {
        try {
        	//最终还是 invoke 方法完成调用
            super.h.invoke(this, m3, (Object[])null);
        } catch (RuntimeException | Error var2) {
            throw var2;
        } catch (Throwable var3) {
            throw new UndeclaredThrowableException(var3);
        }
    }

    static {
        try {
            m1 = Class.forName("java.lang.Object").getMethod("equals", Class.forName("java.lang.Object"));
            m2 = Class.forName("java.lang.Object").getMethod("toString");
            m0 = Class.forName("java.lang.Object").getMethod("hashCode");
            m3 = Class.forName("com.tudou.core.TestInterFace").getMethod("showTest");
        } catch (NoSuchMethodException var2) {
            throw new NoSuchMethodError(var2.getMessage());
        } catch (ClassNotFoundException var3) {
            throw new NoClassDefFoundError(var3.getMessage());
        }
    }
}
```


## Mybatis动态代理

Mybatis 主要实现代理类 `MapperProxy<T>` 核心代码如下

```
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        try {
        	//判断hashCode()、toString()、equals()等方法，将target指向当前对象this
            if (Object.class.equals(method.getDeclaringClass())) {
                return method.invoke(this, args);
            }

            if (this.isDefaultMethod(method)) {
                return this.invokeDefaultMethod(proxy, method, args);
            }
        } catch (Throwable var5) {
            throw ExceptionUtil.unwrapThrowable(var5);
        }


        MapperMethod mapperMethod = this.cachedMapperMethod(method);
        //获取sql生成的对应的模型对象
        return mapperMethod.execute(this.sqlSession, args);
    }

    private MapperMethod cachedMapperMethod(Method method) {
        MapperMethod mapperMethod = (MapperMethod)this.methodCache.get(method);
        if (mapperMethod == null) {
            mapperMethod = new MapperMethod(this.mapperInterface, method, this.sqlSession.getConfiguration());
            this.methodCache.put(method, mapperMethod);
        }

        return mapperMethod;
    }
```    

``` 
public class MapperProxyFactory<T> {
    private final Class<T> mapperInterface;
    private final Map<Method, MapperMethod> methodCache = new ConcurrentHashMap();

    public MapperProxyFactory(Class<T> mapperInterface) {
        this.mapperInterface = mapperInterface;
    }

    public Class<T> getMapperInterface() {
        return this.mapperInterface;
    }

    public Map<Method, MapperMethod> getMethodCache() {
        return this.methodCache;
    }

    protected T newInstance(MapperProxy<T> mapperProxy) {
        return Proxy.newProxyInstance(this.mapperInterface.getClassLoader(), new Class[]{this.mapperInterface}, mapperProxy);
    }

    public T newInstance(SqlSession sqlSession) {
        MapperProxy<T> mapperProxy = new MapperProxy(sqlSession, this.mapperInterface, this.methodCache);
        return this.newInstance(mapperProxy);
    }
}
``` 
