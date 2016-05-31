---
layout:     post
title:      "Leetcode Product of Array Except Self"
subtitle:   "Medium题目 编号238"
date:       2016-04-12 14:00:00
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
Given an array of n integers where n > 1, nums, return an array output such that
 output[i] is equal to the product of all the elements of nums except nums[i].

Solve it without division and in O(n).

For example, given [1,2,3,4], return [24,12,8,6].

Follow up:
Could you solve it with constant space complexity? (Note: The output array does
    not count as extra space for the purpose of space complexity analysis.)
```

#### 翻译

```
给定一个含有n（n > 1)个整数的nums数组，返回一个output数组，其中output[i]的值等于nums数组中
除nums[i]的所有数的乘积。

在O(n)时间复杂度内解决问题，不能使用除法。

例子：给出数组[1,2,3,4], 返回数组[24,12,8,6]
```

#### Tags

`Array`

#### 代码

```
Java：

public int[] productExceptSelf(int[] nums) {
    int length = nums.length;
    if(length == 0) return new int[0];
    int[] result = new int[length];
    int tmp = 1;
    for(int i = 0; i < length; i++){
        result[i] = tmp;
        tmp *= nums[i];
    }

    tmp = 1;
    for(int i = length - 1; i >= 0; i--){
        result[i] *= tmp;
        tmp *= nums[i];
    }
    return result;
}

```

#### 讲解

```
首先构造一个前乘数组result，result中的每一个值result[i]都是nums中0到i-1的所有数的乘积。
然后利用前乘数组构造一个后乘数组，result中的每一个值result[i]都是其与nums中length-1到i+1的乘积。

例子：
[2, 5, 7, 1]
前乘数组：[1, 2, 10, 70]
后乘数组：[35, 14, 10, 70]
```

#### 习题地址

[Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/){:target="_blank"}
