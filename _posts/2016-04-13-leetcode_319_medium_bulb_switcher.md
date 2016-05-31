---
layout:     post
title:      "Leetcode Bulb Switcher"
subtitle:   "Medium题目 编号319"
date:       2016-04-13 12:00:00
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
There are n bulbs that are initially off. You first turn on all the bulbs.
Then, you turn off every second bulb. On the third round, you toggle every
third bulb (turning on if it's off or turning off if it's on).
For the ith round, you toggle every i bulb.
For the nth round, you only toggle the last bulb.
Find how many bulbs are on after n rounds.

Example:

Given n = 3.

At first, the three bulbs are [off, off, off].
After first round, the three bulbs are [on, on, on].
After second round, the three bulbs are [on, off, on].
After third round, the three bulbs are [on, off, off].

So you should return 1, because there is only one bulb is on.
```

#### 翻译

```
有n个初始状态为关的灯泡。
首先，你打开全部灯泡。
然后每两个灯泡关闭一个。
第三轮每隔三个灯泡切换一次开关（关的打开，开的关闭）。
第i轮，每隔i个灯泡切换一次开关。
第n轮，切换第n个开关。
找出n轮之后又多少个灯泡还亮着。
```

#### Tags

`Math` `Brainteaser`

#### 代码

```
Java:

public int bulbSwitch(int n) {
    return (int)Math.sqrt(n);
}

```

#### 讲解

```
很容易的我们可以推断出，如果一个灯泡被切换了偶数次，他最后一定是关闭的。
同理，如果一个灯泡被切换了奇数次，他最后一定是开启的。
对于第i个灯泡，如果他不是一个完全平方数，那他一定有偶数个因子，也就是说他会被切换偶数次。
比如6（1，2，3，6），18（1，2，3，6，9，18）等。
如果他是一个完全平方数，则他只有奇数个因子，也就是说他会被切换奇数次。
比如9（1，3，9），36（1，6，36）等。
即最后开启的灯泡数为n中完全平方数的个数。
我们只需要找出n中有多少个完全平方数即可，而不超过n的完全平方数个数等于不超过n的最大完全平方数的平方根。
```

#### 习题地址
[Bulb Switcher](https://leetcode.com/problems/bulb-switcher/){:target="_blank"}
