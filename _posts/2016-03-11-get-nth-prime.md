---
layout: post
title: "[算法]求第 n 个质数"
subtitle:   ""
date:   2016-03-11
author: "RryLee"
tags:
    - 算法
---

* content
{:toc}

> 该问题来自 [Hankerrank](https://www.hackerrank.com/contests/projecteuler/challenges/euler007)，[欧拉计划](https://projecteuler.net/)的第七题。

# 问题

**Input Format**
First line contains TT that denotes the number of test cases. This is followed by TT lines, each containing an integer, NN.

**Output Format **
Print the required answer for each test case.

    Constraints 
    1≤T≤1031≤T≤103 
    1≤N≤104
    
**Sample Input**

2
3
6

**Sample Output**

5
13
    
# 一些思路

> 关于判断质数就不用多提了。

**第一波**

也就是最直观的一直找下一个质数，将当前是第几个储存在一个变量里面，当 n 和这个变量相等就输出它，进入下一次输入查找。

** 第二波 **

在第一波的基础上有所提升，当然这也是这种提的一个套路，因为我们有 T 组测试，所以你可以讲质数放在一个数组里面，如果 n 小于这个数组的长度，直接返回就可以了，否则，就继续之前的查找。

** 第三波　**

在第二波的基础上，当 n 大于数组长度时，你可以判断下当前查找的数是否在数组中，在就跳过这次循环。

** 第四波 **

在第三波基础上，我们完全可以跳过这个判断n是否存在数组中，只需要讲从数组的最后一个质数 + 2　开始遍历，每次遍历加上 2.

> 剩下的就是看代码了，没一波我都写在代码里了，随便看一下结果，cool!

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1f1t27yboswj210904udfy.jpg)

# 代码

    #include <cstdio>
    #include <cmath>
    #include <vector>
    #include <algorithm>
    
    using namespace std;
    
    typedef unsigned long long ull;
    
    vector<ull> primes; // 第二波:加上的一个向量，和php中的数组一个作用(仅在这里)
    vector<ull>::iterator it;
    
    bool isPrime(ull n) {
        if (n % 2 == 0) {
            return false;
        }
    
        for (ull i = 3; i < sqrt(n) + 1; i += 2) {
            if (n % i == 0) {
                return false;
            }
        }
    
        primes.push_back(n);
    
        return true;
    }
    
    int main() {
        int T, n;
        scanf("%d", &T); // input
        primes.push_back(2); // php: primes[] = 2;
        primes.push_back(3);
    
        while (T --) {
            scanf("%d", &n);
    
            if (n <= primes.size()) { // 第二波
                printf("%llu\n", primes[n - 1]);
    
                continue;
            } else {
                int current = primes.size(); // 第四波: 这里很关键
                for (ull i = primes.back() + 2;i <= 104743; i += 2) {
                    if (isPrime(i)) {
                        current ++;
    
                        if (current == n) {
                            printf("%llu\n", i);
                            break;
                        }
                    }
                }
            }
        }
    
        return 0;
    }
