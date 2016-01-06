---
layout: post
title: "关于javascript对象的一些干货"
subtitle:   "飞向2016"
date:   2016-01-06
author: "RryLee"
tags:
    - javascript
---

* content
{:toc}

# 新年好

大家新年好，先扯一下蛋，2015 年真的是javascript发展的最为壮丽的一年，就我来说，学习了各种新东西，也希望大家都有收获。

> 推荐一篇很给力的关于 js 中 this 的用法，这里是翻译的，原文链接没找到。 [传送门](http://segmentfault.com/a/1190000002640298)

# 关于对象

这里我就 Object 开始展开，都知道在 js 中，对象是一个很特别的存在。

<a class="jsbin-embed" href="http://jsbin.com/jujibod/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.5"></script>

### create object

你可以这样简单的区创建对象并且添加属性

    var obj = {
        firstName: "Rry",
        lastName: "Lee"
    };

你也可以简单的声明一个空对象，然后去添加属性

    var obj = {};

    obj.firstName = "Rry";
    obj["lastName"] = "Lee";

    Object.defineProperty(obj, "age", {
        value: 20
    });

    Object.defineProperties(obj, {
        sex: {
            value: "male"
        },
        email: {
            value: "rrylee@gmail.com"
        }
    });

关于 `obj["lastName"]` 这种写法，更加推荐使用 dot (.) 符号来进行， 也就是 `obj.firstName`

关于 [defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) 和 [defineProperties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperties) 可以自定义的属性的一些权限，可点击链接查看

# Object.create

你可以使用 [Object.create](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create) 这个方法来创建对象，在 ES5 中，我们通过对传入一个函数对象的prototype属性来完成原型链的继承

    var obj2 = {
        firstName: "Rry",
        lastName: "Lee",
        sayName: function() {
            return "My name is " + this.firstName + " " + this.lastName;
        }
    };

    var RryTip = Object.create(obj2, {
        LastName: {
            value: "Tip"
        },
        greet: {
            value: function(person) {
                return "Hello, I am " + person.firstName;
            }
        }
    });

# 函数对象

这 js 中有函数对象和普通对象的区分，看下面的例子体会

    function Person (firstName, lastName) {
        this.firstName = firstName;
        this.lastName = lastName;

        this.sayName = function() {
            return "My name is " + this.firstName + " " + this.lastName;
        };
    }

    Person.prototype.greet = function() {
        return "Hello, I am " + this.firstName;
    }

函数对象特有的 prototype 属性

    var RryLee = new Person("Rry", "Lee");
    var RryTip = new Person("Rry", "Tip");

这样就得到了两个普通对象，它们的 __proto__ 指向 Person 的 prototype!

<a class="jsbin-embed" href="http://jsbin.com/jujibod/embed?js,console">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.5"></script>
