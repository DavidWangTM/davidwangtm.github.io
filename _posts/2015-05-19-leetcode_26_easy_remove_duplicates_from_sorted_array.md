---
layout:     post
title:      "Leetcode Remove Duplicates from Sorted Array"
subtitle:   "Easy题目 编号26"
date:       2015-05-19 14:00:00
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
Given a sorted array, remove the duplicates in place such that each element
appear only once and return the new length.

Do not allocate extra space for another array, you must do this in place with
constant memory.

For example,
Given input array nums = [1,1,2],

Your function should return length = 2, with the first two elements of nums
being 1 and 2 respectively. It does not matter what you leave beyond the new
length.
```

#### 翻译

```
给定一个有序数组，删除所有重复的元素，使得每个元素仅出现一次，返回新的数组长度。
不要使用额外的数组，你必须在连续的内存中完成这项工作。
例如，给定一个输入数组[1, 1, 2], 你的函数应该返回length = 2，
并且数组的前两个元素应该分别为1和2。超过新长度的部分不需要进行考虑。
```

#### tags

`array` `two pointers`

#### 代码

```
C：

int removeDuplicates(int* nums, int numsSize) {
	if (numsSize == 0 || numsSize == 1)
        return numsSize;

	int count = 0;
	int i;
	for(i = 0; i<numsSize; i++){
		if(nums[count] != nums[i])
			nums[++count] = nums[i];
	}
	return ++count;
}
```

#### 讲解

```
本题应用了快慢指针的思想，

count用于向数组的最前部分存储不重复的数字，

i用于检索每一个数字，

如果出现重复的数字，就跳过他，继续向后进行检索，如果出现不同的数字，则count在数组中右移一位后
存储这个新的不重复数字

遍历完整个数组后结束。
```

#### 习题地址

[Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/){:target="_blank"}
