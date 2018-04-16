---
layout:     post
title:      "Mybatis回顾 四"
subtitle:   "Mybatis回顾-实现Mini版本的Mybatis"
date:       2016-11-21 7:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - Mybatis
--- 

# 实现Mini版本的Mybatis

![basic_design_patterns_31](/img/in-post/basic_design_patterns/basic_design_patterns_31.png)


## Model

```
public class TestModel {

	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "TestModel{" +
				"name='" + name + '\'' +
				'}';
	}
}
```


## Mapper接口与实现类

```
public interface TestMapper {

	public TestModel getTestModel(Integer id);

}


public class TestMapperXml {

	public static final Map<String,String> map=new HashMap<String,String>();

	static{
		//模拟xml中的id与sql语句
		map.put("getTestModel", "测试SQL");
	}
}
```

## Executor接口与实现类

```
public interface TestExecutor {

	public <T> T query(String statement,Object parameter);

}

public class TestSimpleExcutor implements TestExecutor{

	public <T> T query(String statement, Object parameter) {
		//JDBC 内容
		TestModel model = new TestModel();
		model.setName(statement + "-入参-" + parameter.toString());
		return (T) model;
	}

}

```

## Handler实现类

```
public class TestMapperHandler implements InvocationHandler {


	private TestSqlSession testSqlSession;

	public TestMapperHandler(TestSqlSession testSqlSession) {
		this.testSqlSession  = testSqlSession;
	}

	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

		return testSqlSession.selectOne(TestMapperXml.map.get(method.getName()),args[0]);
	}
}
```

## SqlSession实现类

```
public class TestSqlSession {

	private TestExecutor excutor = new TestSimpleExcutor();

	public <T> T selectOne(String statement,Object parameter){
		return excutor.query(statement, parameter);
	}

	public <T> T getMapper(Class<T> clas){
		return (T) Proxy.newProxyInstance(clas.getClassLoader(),new Class[]{clas}, new TestMapperHandler(this));
	}

}
```

## 调用类

```
public class TestMainMybatis {

	public static void main(String[] args) {
		TestSqlSession sqlsession = new TestSqlSession();
		TestMapper mapper = sqlsession.getMapper(TestMapper.class);
		TestModel model = mapper.getTestModel(1);
		System.out.println(model.toString());
	}

}
```



