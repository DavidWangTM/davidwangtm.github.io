---
layout:     post
title:      "阿里题目实践"
subtitle:   "阿里题目实践 - 模拟网站被请求"
date:       2018-9-5 23:00:00
author:     "DavidWang"
header-img: "img/post-bg-e2e-ux.jpg"
catalog: true
tags:
    - JAVA
--- 

## 前序

### 题目

题目为模拟网站被请求，然后30分钟只能登陆5次，只考虑单机情况（具体可以理解为：多线程环境，对当前IP段进行数量读取和标记）

- 第一个想法是使用 `synchronized` 来封装方法，使用 `AtomicInteger` 操作数量自增。

- 第二个想法是使用 `ConcurrentHashMap` 做线程安全的读和写操作，使用 `AtomicInteger` 操作数量自增。

#### 创建模型类
 
```
class ModelTest {
   private AtomicInteger integer = new AtomicInteger(0);   
   public AtomicInteger getInteger() {
      return integer;
  }
}
```
#### 创建线程池模拟线程情况

```
	//模拟同时 10000 个线程执行以下方法
final int totalThread = 10000; 
ExecutorService executorService = Executors.newCachedThreadPool(); 
for (int i = 0; i < totalThread; i++) {
    final int num = i;
	executorService.execute(new Runnable() {
		@Override
		public void run() {
		    Show("192.168.1.1", num);
		    Show("192.168.1.2", num);
		    Show("192.168.1.3", num);
		}
	}); 
}
executorService.shutdown();
```


#### synchronized 加 AtomicInteger 实现方法

```
static Map<String, Object> map = new HashMap<String, Object>();

void Show(String name, int num) {
	ModelTest test = new ModelTest();
	test = (ModelTest) map.get(name) != null ? (ModelTest) map.get(name) : test;
	test.getInteger().getAndIncrement();
	synchronized (name) {
		if (test.getInteger().intValue() <= 5){
			System.out.println(Thread.currentThread().getName() + "--" + name + "--" + num + "--未锁定");
		}
	}
	map.put(name, test);
}
```
#### ConcurrentHashMap 加 AtomicInteger 实现方法

```
static ConcurrentHashMap<String, Object> map = new ConcurrentHashMap<String, Object>();

void Show(String name, int num) {
	ModelTest test = (ModelTest)map.get(name);
	if (test == null){
		ModelTest1 new_test = new ModelTest();
		//map.putIfAbsent 为CAS操作
		test = (ModelTest)map.putIfAbsent(name,new_test);
		if (test == null){
			test = new_test;
		}
	}
	test.getInteger().getAndIncrement();
	if (test.getInteger().intValue() <= 5) {
		System.out.println(Thread.currentThread().getName() + "--" + name + "--" + num + "--未锁定");
	}
}
```
 
#### 其他类型

有同学说有不使用 AtomicInteger 而使用 int 来操作吗？
当然可以，使用了自旋和ConcurrentHashMap CAS操作，来保证模型对象是最新操作集。

```
class ModelTest {
   public int integer = 1;
}
```

```
static ConcurrentHashMap<String, Object> map = new ConcurrentHashMap<String, Object>();

void Show(String name, int num) {
	ModelTest oldTest;
	ModelTest newTest;
	while (true) {
		oldTest = (ModelTest1) map1.get(name);
		if (oldTest == null) {
			newTest = new ModelTest1();
			if (map1.putIfAbsent(name, newTest) == null) {
				break;
			}
		} else {
			oldTest.integer += 1;
			newTest = oldTest;
			if (map1.replace(name, oldTest, newTest)) {
				break;
			}
		}
	}
	if (newTest.integer <= 5) {
		System.out.println(Thread.currentThread().getName() + "--" + name + "--" + num + "--未锁定");
	}
}	
```
### 总结

还有很多其他的方法，就不列举了，不过通过这个题目，捡起来很多基础的知识，使用大型框架和第三方库慢慢的对底层方法操作越来越疏忽，日后还是得温习基础知识，在此写内容为自己和读者共勉。