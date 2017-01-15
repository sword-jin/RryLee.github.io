---
layout: post
title: "在 thinkphp 中使用 laravel 的全部组件"
subtitle:   ""
date:   2017-01-15
author: "RryLee"
tags:
    - php
    - laravel
    - illuminate
---

> 作为一个使用 php 作为主力语言的公司，不管怎么说，在一些老的项目中，总会碰到使用 thinkphp 的。那么，热爱 laravel 的你，当你去开发新的 feature 时，大胆的去引入 laravel 的组件吧。不管是对于现在的开发效率，还是日后项目的重构，迁移都是有很大帮助的。那么下文就介绍下我是如何在 tp 项目中去使用 illuminate 的。

# 必知概念

对 laravel 熟悉的同学都知道，laravel 和 tp 最大的区别之一就会 tp 中没有容器这个概念了。关于容器，可以通读一下文档 [Container](https://laravel.com/docs/5.3/container)。在引入 illuminate 到 tp 中，第一步就是需要引入容器。

**Composer** 这个包管理工具也是 tp 中没有的 (据说 5.0 引入了)。引入之后在 tp 的 index.php 文件加上

```php
require_once __DIR__ . '/../vendor/autoload.php';
```

# 开始

这边我使用 tp3.2 来作为例子(其实公司的是 2.7，原理上并不存在任何区别)。

在 tp 中，配置文件是使用 C 函数(这个时候程序已经进入到 tp 中了)来获取，这里就有两个选择了，你可以使用在 tp 的控制器初始化的时候引入你的 illuminate 初始化文件，这样你就不必维护一组单独的配置文件了(也就是 laravel 中的 env)。另外一种情况当然是引入 env 了，优势劣势都很明显，看你对整个项目的把控程度有多少，来采取相应的策略。

# 引入组件

例子引入一下组件

```json
"illuminate/database": "^5.3",
"vlucas/phpdotenv": "^2.4",
"illuminate/events": "^5.3",
"illuminate/filesystem": "^5.3",
"illuminate/cache": "^5.3",
"symfony/var-dumper": "^3.2",
"filp/whoops": "^2.1"
```

> 有几个坑值得注意, 在 tp 中直接申明了两个方法, `e` 和 `dump` 都和 `illuminate/support` 这个包提供的两个全局方法冲突了，我的做法是直接删除 tp 提供的。

# 实现

关于具体实现

代码细节直接参考 [illuminate-in-tp](https://github.com/RryLee/illuminate-in-tp)，基本没有什么太多细节。

> 最后，祝愿你们都能在老的项目中使用上 laravel。
