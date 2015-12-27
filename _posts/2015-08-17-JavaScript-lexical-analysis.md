---
layout: post
title: "JavaScript函数执行词法分析"
date:   2015-08-17
subtitle: ""
author: "RryLee"
tags:
    - javascript
---

这里先给出一个这样问题来引出本次的探讨

    var a = 20;
    function t1 () {
        console.log(a);
        console.log(b);
        var b = 4;
    }
    t1();

---

在JS的笔试题中看到过无数个这种类似的问题，JS函数执行前都会进行一个词法分析，和绝大多数语言一样，都会有这样一个过程，下面是学习JS词法分析的一些总结。

<img class="shadow" src="http://ww3.sinaimg.cn/mw690/baa3278fgw1ev5szlr87lj20ge0hedis.jpg" />

一般来说，一个JS的函数执行都会经过这样一个步骤，下面带分析几个不同的例子来掌握这种分析方法。先拿上面的例子来说：

    /**
     * 分析过程:
     * 1: 生成 AO = {}
     * 2.1: 分析形参，没有
     * 2.2: 接受形参，没有
     * 3: 分析 var 声明，var b, 函数内存在局部变量b 为undified, AO 上没有 b 属性, 则 AO = {b: undefined}
     * 4: 分析函数声明，没有
     *
     *执行:
     * console.log(a); // 20
     * console.log(b); // undifined
     * b = 4;          // 在这个作用域，此时 b 才为 4
     */

---

首先，我们要知道属性在分析变量声明期间，属性只能赋值为 undifined，所以上面的 console.log(b) 才为 undifined，你可以这样思考，将 var b = 4 的 var 去掉，会得到 `Uncaught ReferenceError: b is not defined`，这样就知道了 var 这里的作用。下面看一个稍微复杂一点的例子：

    function t3 (greet) {
        var greet = 'Hello';
        alert(greet);
        function greet () {
        }
        alert(greet);
    }
    t3(null);

这样试着分析以下，是这样的过程

    /**
     * 0: AO = {}
     * 1.1: 分析形参 AO = {greet: undifined}
     * 1.2: 接受形参, AO = {greet: null}
     * 2: 分析greet变量声明, AO已经有了greet属性，因此不做任何影响
     * 3: 分析greet函数声明, AO.greet = function(){}, greet属性被覆盖
     *
     * 执行:
     * greet = 'Hello'; // AO.greet = 'Hello'
     * alert(greet); // Hello
     * alert(greet); // Hello
     */

重点是 `var greet = 'Hello';` ，greet的值在函数执行期间再次被赋值，因此会输出两个 hello

---

总结：在各种笔试题中都会出现这样类似的问题，掌握了词法分析，这类问题也就不是问题了。
