---
layout: post
title: "javascript array length in depth"
subtitle:   ""
date:   2016-01-10
author: "RryLee"
tags:
    - javascript
---

* content
{:toc}

# 必读

感觉没什么可读性，闲的无聊，查阅了一些资料，整理了关于 `js` 数组长度的一个小问题。

# 问题来了

js 数组长度的可变性，为什么可变呢？答案在这里，[Array.prototype.length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length)，看到 `Writable` 为 yes 吧！那么

* 怎么变

* 变成什么样

# 答案

第一个问题很简单，就是它的 `length` 属性会改变。

接下来就需要看一下下面的变化了(注释)

    var a = [1, 2, 3];

    console.log(a.length); // 3

    a[5] = "5";

    console.log(a.length); // 6

第一次变化为 6，其实元素本来应该只有 4 个的，元素的其他未被填充了，值为 `undefined`。

    a["10"] = 10;

    console.log(a.length); // 11

    a["abc"] = "abc";

    console.log(a.length); // 11

    console.log(typeof(a[5])); // string
    console.log(typeof(a[8])); // undefined
    console.log(typeof(a[10])); // number

可以看到对应元素的类型均正确

    a.length = 12;

    console.log(a.length); // 12

这样就直接修改了数组的长度，不够的值为 undefined

    console.log(a)
    // [1, 2, 3, 5: "5", 10: 10, abc: "abc"]
    a
    // [1, 2, 3, undefined × 2, "5", undefined × 4, 10, undefined × 3]

如果继续 for 循环遍历

    for (var i = 0, length = a.length; i < length; i ++) {
        console.log(a[i]);
    }
    1
    2
    3
    undefined
    undefined
    5
    undefined
    undefined
    undefined
    undefined
    10
    undefined
    undefined
    undefined

这样看得话就明确了，**没指定的都是 undefined**，缩短数组

    a.length = 3;

    console.log(a); // [1, 2, 3, abc: "abc"]

    a // [1, 2, 3]

其实这里有一段话对这个__结果__进行了解释，我就不说明了，我接受了这个解释，XD

>If you make the length property smaller than its previous value, the array is truncated, and any elements with array indexes equal to or greater than the new value of the length property are lost.
If you make the length property larger than its previous value, the array is expanded, and any new elements created have the value undefined.

原文贴在后面，谢阅.

# 参考

* [Array.prototype.length -- MND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length)

* [length Property (Array) (JavaScript) -- MSDN](https://msdn.microsoft.com/en-us/library/d8ez24f2(v=vs.94).aspx)
