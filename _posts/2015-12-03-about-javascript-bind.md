---
layout: post
title: "请尝试去使用bind(Js)来改变this"
subtitle: ""
date:   2015-12-03
author: "RryLee"
tag:
    - javascript
---

* content
{:toc}

# 闲扯

写 Javascript 也有很长时间了，说真的像`apply`, `call`这种函数经常会使用，第一次看到`bind`的时候感到疑问，原理Javascript中是存在这个函数的，因为我最对[Vue](http://cn.vuejs.org/)比较熟悉，里面提供一个使用上文`this`的`bind`函数，哦，原来Js里面是存在的。

---

# 概述

使用 `bind()` 方法来创建一个新函数，称为绑定函数，当调用这个绑定函数时，绑定函数会以创建它时传入bind() 方法的第一个参数作为this，传入 bind() 方法的第二个以及以后的参数加上绑定函数运行是本身的参数按照顺序作为原函数的参数来调用原函数。

    fun.bind(thisArg, [, arg1, [, arg2[, ...]]])

---

# Demo

首先你需要去理解上下文，这里有个最简单的例子(以下例子在浏览器中运行)

    console.log(this);
    // Window {top: Window, location: Location, document: document, window: Window, external: Object…}

这里我们得到我们的window对象(即浏览器中的boss)，接下来

    var thing = {
        some: function() {
            console.log(this);
        }
    }
    var some = thing.some;
    some();
    // Object {}

这个时候得到我们的对象，这时候上文就不再是 window，而是thing这个对象，那么，这个时候怎么调整来得到我们的window对象呢

    var self = this;

    var thing = {
        some: function() {
            console.log(self);
        }
    }

    var some = thing.some;
    some();

使用一个变量来保存我们需要的this

> 我们jQery中很喜欢这样来做

那么，使用bind()，将我们的 window 对象传入生成一个绑定对象

    var thing = {
        some: function() {
            console.log(this);
        }
    }

    var some = thing.some;

    some.bind(window)();

### 输出

这边来使用一个对象来生成绑定函数

    var foo = {
        a: 10
    }
    var bar = function() {
        console.log(this.a);
    }
    bar(); // undifined
    var bindBar = bar.bind(foo);
    bindBar(); // 10

### 初始参数

    function slug() {
        return Array.prototype.join.call(arguments);
    }

    var str1 = slug(1, 2, 3); // 1,2,3

    var defaultSlug = slug.bind(undefined, 'Rry');

    var str2 = defaultSlug('RryLee', 'RryTip'); // Rry,RryLee,RryTip

这样就之后传递的参数就会在初始参数之后。

或许这样看起来更加直接

    function Foo () {
    }
    Foo.prototype.foo = "Bar";

    function logFoo(str) {
        console.log(str, this.foo);
    }

    var foo = new Foo();
    logFoo.bind(foo)("Hi RryTip");

# 总结

这里基本只讲了bind的基本使用，但是从其他前端框架的发展来看，bind的优势有很多的，它让你的代码变得更加简洁，避免了很多不必要的变量(存放上文this)，所以，还请在工作中多去尝试bind

> 参考 [Function.prototype.bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
