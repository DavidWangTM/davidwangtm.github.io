---
layout:     post
title:      "Leetcode Maximum Product of Word Lengths"
subtitle:   "Medium题目 编号318"
date:       2016-04-19 12:00:00
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
Given a string array words, find the maximum value of length(word[i]) * length(word[j])
where the two words do not share common letters. You may assume that each word will
contain only lower case letters. If no such two words exist, return 0.

Example 1:
Given ["abcw", "baz", "foo", "bar", "xtfn", "abcdef"]
Return 16
The two words can be "abcw", "xtfn".

Example 2:
Given ["a", "ab", "abc", "d", "cd", "bcd", "abcd"]
Return 4
The two words can be "ab", "cd".

Example 3:
Given ["a", "aa", "aaa", "aaaa"]
Return 0
No such pair of words.
```

#### 翻译

```
给定一个string数组words，找出没有重复字母的最大length(word[i]) * length(word[j])。
你可以假设每个单词只有小写字母，如果没有这样的两个单词，返回0.
```

#### Tags

`Bit Manipulation`

#### 代码

```
Java:

public int maxProduct(String[] words) {
    int max = 0;
    int length = words.length;
    int[] wordList = new int[length];
    for(int i = 0; i < length; i++)
        for(char chr: words[i].toCharArray())
            wordList[i] |= 1 << (int)(chr - 'a');

    for(int i = 0; i < length; i++){
        for(int j = i + 1; j < length; j++){
            if((wordList[i] & wordList[j]) == 0)
                max = Math.max(max, words[i].length() * words[j].length());
        }
    }

    return max;
}

```

#### 讲解

```
int类型有32位，小写字母只有26位，所以可以用一个int型存储所有小写字母，然后通过与操作可以知道两个
单词是否有重复，进而可以算出长度积的最大值。
```

#### 习题地址
[Maximum Product of Word Lengths ](https://leetcode.com/problems/maximum-product-of-word-lengths/){:target="_blank"}
