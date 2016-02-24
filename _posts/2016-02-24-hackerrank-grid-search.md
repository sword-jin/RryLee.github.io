---
layout: post
title: "查找矩阵的问题"
subtitle:   ""
date:   2016-02-24
author: "RryLee"
tags:
    - 算法
---

* content
{:toc}

> 最近一直在 [hackerrank](https://www.hackerrank.com) 做练习 challenges，其中 [The Grid Search](https://www.hackerrank.com/challenges/the-grid-search) 这个问题花了几个小时时间解决，算法太囧，不过，要说的是， hackerrank 可以容许你使用任何享用的语言来解决问题，最近用 php 来写还是不错的。

# 问题描述

问题大概是这样的，给你一个二维矩阵A，查找二维矩阵B是否存在A中。

A

```php
1234567890
0987654321
1111111111
1111111111
2222222222
```

B
```php
876543
111111
111111
```

然后加上一些限制条件(可忽略)

# 方案

接受输入时，直接将矩阵的一行赋值给数组的一个元素

* 遍历 A 矩阵 (foreach A as a)
* 查找 B 矩阵的第一行在当前a 的位置，记录位置，直到查找不到为止
* 接着查找 B 矩阵的每一行都是否存在，且位置相同。

这样讲有点抽象，不过我觉得有兴趣的同学可以去做着玩一下。

# 代码

    <?php

    $handle = fopen ("php://stdin", "r");
    fscanf($handle, "%d", $t);

    for ($a0 = 0; $a0 < $t; $a0++) {
        $big = array();
        $small = array();

        fscanf($handle, "%d %d", $R, $C);
        for ($big_i = 0; $big_i < $R; $big_i++) {
           fscanf($handle, "%s", $big[]);
        }

        fscanf($handle, "%d %d", $r, $c);
        for ($small_i = 0; $small_i < $r; $small_i++) {
           fscanf($handle, "%s", $small[]);
        }

        echo gridExists($big, $small) ? "YES\n" : "NO\n";
    }

    function gridExists(array $big, array $small) {
        $exists = false;
        $match = [];

        foreach ($big as $key => $bigLine) {
            if ($exists) break;

            $start = 0;

            while (($position = strpos($bigLine, $small[0], $start)) !== false) {
                if ($exists) break;

                $match = [$key, $position];
                // $start += strlen($small[0]);

                for ($i = 1, $j = $match[0] + 1; $i < count($small); $i ++, $j ++) {
                    $position = strpos($big[$j], $small[$i], $start);

                    if ($position === false || $position !== $match[1]) {
                        $exists = false;
                        break;
                    } else {
                        $exists = true;
                    }
                }

                $start ++;
            }
        }

        return $exists;
    }
