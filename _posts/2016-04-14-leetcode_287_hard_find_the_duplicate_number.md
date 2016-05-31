---
layout:     post
title:      "Leetcode Find the Duplicate Number"
subtitle:   "Hard题目 编号287"
date:       2016-04-14 12:00:00
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
Given an array nums containing n + 1 integers where each integer is between 1
and n (inclusive), prove that at least one duplicate number must exist.
Assume that there is only one duplicate number, find the duplicate one.

Note:
You must not modify the array (assume the array is read only).
You must use only constant, O(1) extra space.
Your runtime complexity should be less than O(n²).
There is only one duplicate number in the array,
but it could be repeated more than once.
```

#### 翻译

```
给定一个包含n+1个整数的nums数组，每个数字都是从1到n（包括n），则一定有一个重复的数字存在。
假设只有一个重复的数字，找出这个数字。

注意：
你不能改变数组结构（假设数组是只读的）。
你只能使用O(1)的空间。
时间复杂度必须小于O(n²)。
只有一个重复的数字，但是这个数字可以重复出现多次。
```

#### Tags

`Array` `Two Pointers` `Binary Search`

#### 代码

```
Java:

public int findDuplicate(int[] nums) {
    int length = nums.length;
    if(length < 1) return -1;
    int low = 1, high = length, count, mid;
    while(low < high){
        count = 0;
        mid = low + (high - low) / 2;
        for(int num: nums)
            if(num <= mid)
                count++;
        if(count > mid)
            high = mid;
        else
            low = mid + 1;
    }

    return low;
}

```

#### 讲解

```
数组中的数字是从1到n的，且只有一个数字是重复的。
取mid = n/2作为中间数，若比mid小的数超过一半，说明重复的数字在[1, mid]中，
同理，若比mid大的数超过一半，说明重复的数字在(mid, n]中，
以上述过程作为判断条件则可找出重复的数字。
```

#### 习题地址
[Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/){:target="_blank"}
