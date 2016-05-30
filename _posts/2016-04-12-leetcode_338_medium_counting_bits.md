---
layout:     post
title:      "Leetcode Counting Bits"
subtitle:   "Medium题目 编号338"
date:       2016-04-12 13:00:00
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
Given a non negative integer number num. For every numbers i in the
range 0 ≤ i ≤ num calculate the number of 1's in their binary representation
and return them as an array.

Example:
For num = 5 you should return [0,1,1,2,1,2].

Follow up:

It is very easy to come up with a solution with run time O(n*sizeof(integer)).
But can you do it in linear time O(n) /possibly in a single pass?
Space complexity should be O(n).
Can you do it like a boss? Do it without using any builtin function
like __builtin_popcount in c++ or in any other language.
```

#### 翻译

```
给定一个非负整数num，针对每一个0 ≤ i ≤ num的i，计算i的二进制表示中含有的1的数量，返回一个数组。

样例：
如果num = 5，你应该返回[0,1,1,2,1,2]。
```

#### 提示

```
1. You should make use of what you have produced already.
2. Divide the numbers in ranges like [2-3], [4-7], [8-15] and so on. And try to generate new range from previous.
3. Or does the odd/even status of the number help you in calculating the number of 1s?

1. 你可以利用已经生成的数据。
2. 将数据分成像[2-3], [4-7], [8-15]的部分。试着利用旧的数据生成新的数据。
3. 计算1的个数的过程中，数字的奇偶性能否提供帮助？
```

#### Tags

`Dynamic Programming` `Bit Manipulation`

#### 代码

```
Java：

public int[] countBits(int num) {
    if(num < 0) return new int[]{0};
    int[] result = new int[num + 1];
    result[0] = 0;
    if(num > 0)
        result[1] = 1;
    for(int i = 2; i < num + 1; i++)
        result[i] = result[i>>1] + result[i&1];
    return result;
}

```

#### 讲解

```
每个数字的二进制1的个数相当于他右移一位的数字的1的个数加上其二进制表示的最左端数字
举例：
5： 101
10： 1010 右移一位为101，加上0，为2个
11： 1011 右移一位为101，加上1，为3个
23： 10111 右移一位为1011，加上1，为4个
```

#### 习题地址
[Counting Bits](https://leetcode.com/problems/counting-bits/){:target="_blank"}
