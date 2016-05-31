---
layout:     post
title:      "Leetcode Missing Number"
subtitle:   "Medium题目 编号268"
date:       2016-04-13 14:00:00
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
Given an array containing n distinct numbers taken from 0, 1, 2, ..., n, find the one
that is missing from the array.

For example,
Given nums = [0, 1, 3] return 2.
Given nums = [3, 2, 0] return 1.

Note:
Your algorithm should run in linear runtime complexity. Could you implement it using
only constant extra space complexity?
```

#### 翻译

```
给定一个包含了n个唯一数字的数组，n的取值是从0到n，找出这个数组缺失的数字。

例如，
给定数组 nums = [0, 1, 3] 返回 2。
给定数组 nums = [3, 2, 0] 返回 1。

注意：
你的算法应该是线性时间复杂度。你能够只使用常数空间复杂度的空间来完成这个题目吗？
```

#### Tags

`Array` `Math` `Bit Manipulation`

#### 代码

```
Java:

public int missingNumber(int[] nums) {
    int sum1 = 0, sum2 = 0, length = nums.length;
    for(int i = 0; i < length; i++){
        sum1 += i;
        sum2 += nums[i];
    }
    return Math.abs(sum2 - sum1 - length);
}

```

#### 讲解

```
因为数组是有序的，所以我们可以通过计算数据元素下标和元素值的差值得出缺失数据相对于数组最大数字侧
的距离。
例如，
nums = [0,1,2,3,5,6]，其下标和为0+1+2+3+4+5 = 15，其元素和为17，则缺失数据距离右侧距离
为2，缺失的数据应该为nums.length - 2 = 6 - 2 = 4。
nums = [5,4,3,2,0],其下标和为0+1+2+3+4 = 10，其元素和为14，则缺失数据距离左侧距离为4，
缺失的数据应该为nums.length - 4 = 5 - 4 = 1。
```

#### 习题地址
[Missing Number](https://leetcode.com/problems/missing-number/){:target="_blank"}
