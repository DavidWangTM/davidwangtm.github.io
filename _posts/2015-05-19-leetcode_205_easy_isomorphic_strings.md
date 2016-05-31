---
layout:     post
title:      "Leetcode Isomorphic Strings"
subtitle:   "Easy题目 编号205"
date:       2015-05-19 16:00:00
author:     "DavidWang"
header-img: "img/post-bg-digital-native.jpg"
catalog: true
tags:
    - Leetcode
    - Algorithm
---

> “[Leetcode](https://leetcode.com/)是一个强大的OJ网站，很多公司的面试题目都可以在这里找到”

#### 题目

```
Given two strings s and t, determine if they are isomorphic.

Two strings are isomorphic if the characters in s can be replaced to get t.

All occurrences of a character must be replaced with another character while
preserving the order of characters. No two characters may map to the same
character but a character may map to itself.

For example,

Given "egg", "add", return true.
Given "foo", "bar", return false.
Given "paper", "title", return true.
```

#### 翻译

```
给定两个字符串s和t，判断它们是否是同构的。
如果字符串s可以通过字符替换的方式得到字符串t，则称s和t是同构的。
字符的每一次出现都必须被其对应字符所替换，同时还需要保证原始顺序不发生改变。
两个字符不能映射到同一个字符，但是字符可以映射到其本身。
测试样例如题目描述。
```

#### 注意

```
You may assume both s and t have the same length.

可以假设s和t等长。
```

#### Tags

`Hash Table`

#### 代码

```
C：

int isIsomorphic(char* s, char* t) {
    //因为z的ascii码是122，map['z'] 也就是map[122],所以至少需要123的空间。
	char mapST[123] = {};  
	char mapTS[123] = {};
	int len = strlen(s);

	for(int i = 0; i < len; i++){
		if(mapST[s[i]] == 0 && mapTS[t[i]] == 0){
			mapST[s[i]] = t[i];
			mapTS[t[i]] = s[i];
		}else
			if(mapST[s[i]] != t[i] || mapTS[t[i]] != s[i])
				return 0;
	}

	return 1;
}

```

#### 讲解

```
使用两个哈希表mapST和mapTS维护两个字符串中字符的映射关系。
mapST[s[i]] = t[i]
mapTS[t[i]] = s[i]
当出现映射不一致的情形时，返回0
否则返回1
```

#### 习题地址

[Isomorphic Strings](https://leetcode.com/problems/isomorphic-strings/){:target="_blank"}
