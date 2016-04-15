---
layout: post
title: "斑马难题Step by Step"
subtitle:   ""
date:   2016-03-30
author: "RryLee"
tags:
    - 算法
---

* content
{:toc}

> 在[exercism.io](http://exercism.io/exercises/python/zebra-puzzle/readme)被这个 [Zebra Puzzle](https://en.wikipedia.org/wiki/Zebra_Puzzle) 难住了。这里一步一步的解决。。。

# 问题描述

```plain
1.There are five houses.
2.The Englishman lives in the red house.
3.The Spaniard owns the dog.
4.offee is drunk in the green house.
5.The Ukrainian drinks tea.
6.The green house is immediately to the right of the ivory house.
7.The Old Gold smoker owns snails.
8.Kools are smoked in the yellow house.
9.Milk is drunk in the middle house.
10.The Norwegian lives in the first house.
11.The man who smokes Chesterfields lives in the house next to the man with the fox.
12.Kools are smoked in the house next to the house where the horse is kept.
13.The Lucky Strike smoker drinks orange juice.
14.The Japanese smokes Parliaments.
15.The Norwegian lives next to the blue house.
```
fang
求 Water 的房间和 Zebra 的房间

# 分析

首先，每个房间有 5 个属性，加上上面的若干条件限制。

属性

    color  --  $red, $green, $ivory, $yellow, $blue
    Nationality (国家)  --  $Englishman, $Spaniard, $Ukrainian, $Norwegian, $Japanese
    Drink -- $coffee, $tea, $milk, $oj, $WATER
    Smoke -- $OldGold, $Kools, $Chesterfields, $LuckyStrike, $Parliaments
    Pet -- $dog, $snail, $fox, $horse, $ZEBRA

需要在满足所有条件的情况下讲这五个属性分给五个房间，大概就是这么一个思路。
那么我们一共有 5! ** 5 种情况。

先将房间和这些属性都抽象成 1,2,3,4,5

这里拿第二个条件说，就是 Englishman == red，因为他们都属于一个房间的属性，所以值必然相等。

当然，还有 6 , 11 这种条件，我们只需要加上两个方法即可。

```php
/**
 * Determinate the right of relation.
 *
 * @param  int $n
 * @param  int $m
 *
 * @return boolean
 */
function rightOf($n, $m) {
    return $n == $m + 1;
}
 
/**
 * Determinate the neighbouring relations
 *
 * @param  int $n
 * @param  int $m
 *
 * @return boolean
 */
function nextTo($n, $m) {
    return abs($n - $m) == 1;
}
```

# 代码

<script src="http://ideone.com/e.js/dKElR2" type="text/javascript" ></script>
