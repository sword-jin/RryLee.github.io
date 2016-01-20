---
layout: post
title: "[笔记]深入JSON.stringify"
subtitle:   ""
date:   2016-01-20
author: "RryLee"
tags:
    - php
    - Eloquent
---

* content
{:toc}

# JSON.stringify

> [mdn doc](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)


`JSON.stringify(value[, replacer[, space]])`

就是将传入的 `value` 转化为 `JSON` 字符串，和`php`中的序列化有点像，不过有些细节，一一道来。

### 直接转化对象

    var input = {
        name: 'RryLee',
        age: 20
    };
    JSON.stringify(input); // '{"name":"RryLee","age":20}'

### 添加space

`space` 为可选参数，`String` 或 `Number`

      var input = {
          name: 'RryLee',
          age: 20
      }
      JSON.stringify(input, null, 2);
      /*
      "{
        \"name\": \"RryLee\",
        \"age\": 20
      }"
      */
      JSON.stringify(input, null, 2);
      /*
      "{
      RryTip\"name\": \"RryLee\",
      RryTip\"age\": 20
      }"
      */

### 添加 Replacer 方法

    var input = {
        time: new Date('1994-12-01')
    }
    function timeReplacer(key, value) {
      if (this[key] instanceof Date) {
        return value.substring(0, 10);
      }
      return value;
    }
    JSON.stringify(input, timeReplacer); // "{\"time\":\"1994-12-01\"}"

### 添加 Replacer 数组

    var input = {
        name: 'RryLee',
        age: 20,
        sex: 'male'
    }
    JSON.stringify(input, ['name', 'age']); // "{\"name\":\"RryLee\",\"age\":20}"

### Enumrable 属性

    var input = Object.create(null, {
        name: {
          enumerable: true,
          value: 'RryLee'
        },
        age: {
          value: 20
        }
    });
    var result = JSON.stringify(input); // '{"name":"RryLee"}'

### toJSON

这里感觉和 `php` 中的 `__sleep` 这个魔术方法很像

    var input = {
        foo: 'foo',
        toJSON: function() {
          return 'bar';
        }
    }
    JSON.stringify(input); // '"bar"'

### Symbol

和想象一样，`Symbol`不能被找到

> [Object.getOwnPropertySymbols()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols)

    var input = {foo: 'bar'};
    input[Symbol('bar')] = 'bar';
    var expected = '{"foo":"bar"}';
    JSON.stringify(input); // '"foo":"bar"'

# 总结

基本算是解开了 `JSON.stringify` 的全部疑惑



