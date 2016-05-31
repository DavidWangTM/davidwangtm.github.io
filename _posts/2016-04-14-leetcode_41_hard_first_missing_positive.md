---
layout:     post
title:      "Leetcode First Missing Positive"
subtitle:   "Hard题目 编号41"
date:       2016-04-18 12:00:00
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
Given an unsorted integer array, find the first missing positive integer.

For example,
Given [1,2,0] return 3,
and [3,4,-1,1] return 2.

Your algorithm should run in O(n) time and uses constant space.
```

#### 翻译

```
给定一个未排序的整数数组，找出第一个缺失的正整数。

例如
给出数组[1,2,0]，返回3
给出数组[3,4,-1,1]，返回2

你的算法时间复杂度应该是O(n)，并且只使用常数大小的空间
```

#### Tags

`Array`

#### 代码

```
Java:

public int firstMissingPositive(int[] nums) {

    int i = 0, n = nums.length;
    while (i < n) {
        // If the current value is in the range of (0,length) and it's not at its correct position,
        // swap it to its correct position.
        // Else just continue;
        if (nums[i] >= 0 && nums[i] < n && nums[nums[i]] != nums[i])
            swap(nums, i, nums[i]);
        else
            i++;
    }
    int k = 1;

    // Check from k=1 to see whether each index and value can be corresponding.
    while (k < n && nums[k] == k)
        k++;

    // If it breaks because of empty array or reaching the end. K must be the first missing number.
    if (n == 0 || k < n)
        return k;
    else   // If k is hiding at position 0, K+1 is the number.
        return nums[0] == k ? k + 1 : k;

}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
}

```

#### 讲解

```
将数组中每个数字放到他应该存在的位置上，如果该位置不是它本身，则说明该位置是缺失的第一个正整数，
如果从1循环到结尾，每个数字都是对应的，说明第0位或者k就是缺失的第一个正整数了。
```

#### 习题地址
[First Missing Positive](https://leetcode.com/problems/first-missing-positive/){:target="_blank"}
