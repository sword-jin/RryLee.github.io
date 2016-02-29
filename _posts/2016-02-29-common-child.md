---
layout: post
title: "[Common Child]最长公共子序列问题"
subtitle:   ""
date:   2016-02-29
author: "RryLee"
tags:
    - 算法
---

* content
{:toc}

> 题目来自 [harkerrank](https://www.hackerrank.com/challenges/common-child)

# 问题
两个字符串 a 和 b是等长的，求共同的最长子字符串 S。

例如

    Sample Input 1:
    HARRY
    SALLY
    
    Sample Output 1:
    2
    
    Longest possible string is "AY"
    
    Sample Input 2:
    ABCDEF
    FBDAMN
    
    Sample Output 2:
    2
    
    Longest possible string is "BD"
    
# 思路

[(Dynamic Programming)动态规划](https://en.wikipedia.org/wiki/Dynamic_programming)

这是思考时画的图，可对照代码参考

<img class="img-shadow" src="http://ww2.sinaimg.cn/mw690/baa3278fgw1f1g8fgs7l8j20t00zkq5j.jpg">

# 代码

[https://github.com/RryLee/HackerRank-Algorithms/blob/master/string/13-common-child.cpp](https://github.com/RryLee/HackerRank-Algorithms/blob/master/string/13-common-child.cpp)
