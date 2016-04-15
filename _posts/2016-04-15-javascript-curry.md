---
layout: post
title: "JavaScript curry详解"
subtitle:   ""
date:   2016-04-15
author: "RryLee"
tags:
    - javascript
---

* content
{:toc}

> 在 JavaScript 代码中经常会看到 curry 的写法，这里我一直都不知道中文怎么解释，姑且就是 curry 吧。

# curry

最简单的例子是这样的，在函数式编程中很常见的写法。

```js
var add = a => b => a + b
var addOne = add(1)
console.log(addOne(2)) // 3
```

好了，在 add 方法中形成了一个闭包，其中 addOne 所处在的环境为 a = 1.

> 关于闭包的函数和环境，可以查看，[mdn closure](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)

# lodash

通过下面例子看看 lodash 的强大之处

<a class="jsbin-embed" href="http://jsbin.com/koxaka/embed?js,console">JS Bin on jsbin.com</a><script src="https://static.jsbin.com/js/embed.min.js?3.35.12"></script>

# 实现

在有了一定了解之后，你一定要手动实现上面这种方法。

<a class="jsbin-embed" href="http://jsbin.com/loxoru/embed?js,console">JS Bin on jsbin.com</a><script src="https://static.jsbin.com/js/embed.min.js?3.35.12"></script>

接下来就可以随意码出 curry 代码了。
