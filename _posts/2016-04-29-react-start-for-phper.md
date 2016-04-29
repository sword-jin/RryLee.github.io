---
layout: post
title: "给 phper 的 react 快速入门介绍[ES6]"
subtitle:   ""
date:   2016-04-29
author: "RryLee"
tags:
    - 算法
---

* content
{:toc}

> 最近在学习 [React](facebook.github.io/react/index.html)，这里根据[官方教程](http://facebook.github.io/react/docs/tutorial.html)做了一些改进，也就是 `react es6` 的版本。

# 官方教程

看了下官方教程，分10多个步骤完成了一个迷你的评论功能，那么，这样为啥要重新写了。

* 模块化，继承 react component 实现组件模块化，也是 react 开发的标准
* ES6，不用多说

# php 脚本

这里需要有*获取评论*和*储存评论*的两个功能，所以使用 `php` 飞快的写一个小脚本，当然，也需要用到数据库，这个适合， sqlite 就太适合不过了。

```sql
# 新建好 db.sqlite 之后，创建数据表
CREATE TABLE comments (id INTEGER primary key AUTOINCREMENT, author char(50) not null, text text not null);
```

php 脚本大致是这样:

<script src="http://ideone.com/e.js/3iZgME" type="text/javascript" ></script>

其中，我们在本地开发时，前后端服务器是分开的，所以需要允许跨域。

> 代码细节不用多说，这样只需要 post 和 get 两个接口，重点在 react.

# 工具

作为一个现代开发者，你当然需要一套方便的工具来前端自动化，这样可以选择 [laravel-elixir](https://laravel.com/docs/5.2/elixir) 作为这个工具，有 `laravel` 基础的同学使用起来应该相当简单。这里我使用 [webpack](https://webpack.github.io/) 来构建。

# React

在 react es6 中，通过继承 `React.component` 来创建组件，一个 helloworld 的组件是这样的

```js
class HelloWorld extends React.component {
    render() {
        return (
            <h1>Hello World</h1>
        )
    }
}
```

这样的代码对 php 开发者也更加友好。

> 剩下的事情就是查看官方教程之后，直接看这个项目代码了，[地址](https://github.com/RryLee/react-start-for-phper)

参考 [http://egorsmirnov.me/2015/05/22/react-and-es6-part1.html](http://egorsmirnov.me/2015/05/22/react-and-es6-part1.html)
