---
layout: post
title: "ES6中的字符串模版详解"
subtitle: ""
author: "RryLee"
date:   2015-12-02
tags:
    - javascript
    - es6
---

* content
{:toc}

# 关于

模版字符串是es6中一个非常重要的特性，除了我们能使用反撇号字符 ` 代替普通字符串的引号 ' 或 "，还有更加好用的特点，So，一起看看。

---

# 拼接字符串

在之间的 es5 中我们会这样来拼接字符串

    var greeting = 'hi';
    var name = 'es6';

    var string = greeting + ', ' + name;
    console.log(string); // hi, es6

然后，在es6中，你可以这样来写，和PHP有些相似

    var string = `${greeting}, ${name}`;

---

# 换行空格

在之前如果你想换行输出，你需要这样

    var greeting = 'hi';
    var name = 'es6';

    var string = greeting + "\n" + name;

在es6中，模版的格式会原样输出，你可以按你的需求随意输出

    var string = `${greeting}
    ${name}`;

---

# 运算

在模版中也是可以各种运算的

    var x = 1;
    var y = 2;

    var string = `${x} + ${y} = ${x + y}`;
    // 1 + 2 = 3

    var x = 1;
    var y = '2';

    var string = `${x} + ${y} = ${x + y}`;
    // 1 + 2 = 12

---

# 自定义tag

    function age(strings, ...values) {
      if (values[0] == 'Rry') {
        values[1] = 21;
      } else {
        values[1] = 'unknow';
      }

      return `${strings[0]}${values[0]}${strings[1]}${values[1]}${strings[2]}`
    }
    var name = 'Rry';
    var message = age`I\'m ${name} and ${""} years old`;
    // I'm Rry and 21 years old
    // I'm Jack and unknow years old

---

# 总结

es6中的字符串模版是一个比较简单的新特性，学习理解都比较轻松，最重要的是它的强大可以让你轻松完成很多以前的工作。
