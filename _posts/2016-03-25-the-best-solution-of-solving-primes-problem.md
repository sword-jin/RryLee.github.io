---
layout: post
title: "最简求质数算法"
subtitle:   ""
date:   2016-03-25
author: "RryLee"
tags:
    - 算法
---

* content
{:toc}

> 原来这么多年，我的求质数算法一直是很蠢的，包括之前在内的几篇同类博客中的代码，那种解法是不可取得。关于，最简算法，参考 [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)

# 简单分析

![](https://upload.wikimedia.org/wikipedia/commons/b/b9/Sieve_of_Eratosthenes_animation.gif)

盗了维基的一张图片。

如果你没看清楚，我们可以这样来分析整个整个步骤。

求 16 以内的所有质数

2 3 4 5 6 7 8 9 10 11 12 13 14 15 16

第一步，处理所有被2整除的(从4(2 ** 2)开始)

2 3 5 7 9 11 13 15

第一步，处理所有被3整除的(从9(3 ** 2)开始)

2 3 5 7 11 13


# Code

这是python版本

<script src="http://ideone.com/e.js/UAyaAz" type="text/javascript" ></script>

这是 php 版本

<script src="http://ideone.com/e.js/oLTVb1" type="text/javascript" ></script>

# 总计

给所有 php 的同学。
