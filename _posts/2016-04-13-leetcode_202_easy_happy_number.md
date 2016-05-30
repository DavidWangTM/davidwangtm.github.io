---
layout:     post
title:      "Leetcode Happy Number"
subtitle:   "Easy题目 编号202"
date:       2016-04-13 13:00:00
author:     "Wenzhiquan"
header-img: "img/post-bg-digital-native.jpg"
catalog: true
tags:
    - Leetcode
    - Algorithm
---

> “[Leetcode](https://leetcode.com/)是一个强大的OJ网站，很多公司的面试题目都可以在这里找到”

#### 题目

```
Write an algorithm to determine if a number is "happy".

A happy number is a number defined by the following process:
Starting with any positive integer, replace the number by the sum of the squares
of its digits, and repeat the process until the number equals 1 (where it will stay),
or it loops endlessly in a cycle which does not include 1.
Those numbers for which this process ends in 1 are happy numbers.

Example: 19 is a happy number

1² + 9² = 82
8² + 2² = 68
6² + 8² = 100
1² + 0² + 0² = 1
```

#### 翻译

```
写一个算法判断一个数是否为“happy”的。
一个happy number由下列程序进行判断：
初始为一个正整数，用这个正整数的每一位数字的平方和代替原数，
重复这个做法直到数字为1，或者进入一个不为1的无限循环。
最后能够达到1的数字就是happy number。
```

#### Tags

`Hash Table` `Math`

#### 代码

```
Java Math：

public boolean isHappy(int n) {
    if(n == 0) return false;
    int sum = 0, tmp = 0;
    while(n != 1){
        sum = 0;
        tmp = n;
        while(tmp > 0){
            sum += Math.pow(tmp % 10, 2);
            tmp /= 10;
        }
        n = sum;
        if(n < 10 && n != 1 && n != 7)
            return false;
    }
    return true;
}
```

```
Java Hash Table:

public boolean isHappy(int n) {
    if(n == 0) return false;
    HashSet<Integer> hashSet = new HashSet<>();
    int sum = 0, tmp = 0;;
    while(n != 1 && n != 7){
        sum = 0;
        tmp = n;
        while(tmp > 0){
            sum += Math.pow(tmp % 10, 2);
            tmp /= 10;
        }
        n = sum;
        if(hashSet.contains(n)) return false;
        hashSet.add(n);
    }
    return true;
}
```

#### 讲解

```
很容易的我们可以推断出，如果一个数为个位数，则只有1和7才是happy number，而任何数字经过有限次
的转换一定会变成个位数，如果变成的个位数不为1和7，则不是happy number，反之则是。
```

#### 习题地址
[Happy Number](https://leetcode.com/problems/happy-number/){:target="_blank"}
