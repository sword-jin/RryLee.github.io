---
layout: post
title: "快速打造帅气的sublime编辑器"
date:   2015-10-16 20:20
categories: 环境
excerpt: 快速打造帅气的sublime编辑器
---

* content
{:toc}

# 常用包

像我有时候不在自己电脑上操作，那么就需要很快的打造完美的sublime环境了。

下面是一些常用的包，但是我在其他同类文章简单的比较少的工具，常用的就不多说了。

* [Laravel 5 Artisan](https://packagecontrol.io/packages/AdvancedNewFile)

> 用来代替命令工具执行 laravle artisan 命令。Ctrl + Shift + P 后查找命令.

![](http://7xly6p.com1.z0.glb.clouddn.com/first.gif)

* [AdvancedNewFile](https://packagecontrol.io/packages/AdvancedNewFile)

> 快速创建文件，建议将快捷键改为 CTRL + N, `:`代表当前所在文件目录哦!

![](http://7xly6p.com1.z0.glb.clouddn.com/newfile.gif)

* [Sublime Text (3) for PHP Developers](https://mattstauffer.co/blog/sublime-text-3-for-php-developers)

> 这是国外大牛 `Matt Stauffer` 的一篇介绍sublime中php插件的

这是我的一些配置文件，可以参考下：

    [
        { "keys": ["f6"], "command": "expand_fqcn" },
        { "keys": ["shift+f6"], "command": "expand_fqcn", "args": {"leading_separator": true} },
        { "keys": ["f5"], "command": "find_use" },
        { "keys": ["f4"], "command": "import_namespace" },
        { "keys": ["shift+f12"], "command": "goto_definition_scope" },
        { "keys": ["ctrl+1"], "command": "insert_php_constructor_property" },
        { "keys": ["ctrl+n"], "command": "advanced_new_file_new"},
        { "keys": ["alt+1"], "command": "toggle_side_bar" },
    ]

# 动态模版

使用 sublime 创建自己的模版也是方便，下面介绍下使用技巧：

一个简单的 class 模版。

![](http://7xly6p.com1.z0.glb.clouddn.com/template1.gif)

会用？那么来点有意思的，不知道有没有用过 sublime 的正则表达式查找替换功能，其实在其他好多地方正则都有用到。

![](http://7xly6p.com1.z0.glb.clouddn.com/template2.gif)

    <snippet>
        <content><![CDATA[
    <!-- ${1/(.+)/\u$1/g} field -->
    <div class="form-group">
        <label for="${1}">${1/(.+)/\u$1/g}</label>
        <input type="text" name="${1}" id="${1}" value="">
    </div>
    ]]></content>
        <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
        <tabTrigger>textfield</tabTrigger>
        <!-- Optional: Set a scope to limit where the snippet will trigger -->
        <!-- <scope>source.python</scope> -->
    </snippet>

好吧，动态模版就是这么酷！

# 完美的主题

最近来谷歌的 material design 越来越流行，sublime中的[Material theme](https://packagecontrol.io/packages/Material%20Theme) 也做得很好看，但个人总感觉色彩有一些太亮，不符合我喜欢的暗黑风格，也是可以随便改改的。

个人比较喜欢 facebook 这款颜色，但是不足的是背景和 material theme 的主题有点不搭调，这个时候可以在sublime的 `Colorsublime - Themes` 文件夹中找到 Facebook.tmTheme 这个文件，修改 background 即可，还有一些小的细节也可以自己 diy，配置是xml文件很易读懂。

> facebook 配色可以安装 [Colorsublime](https://packagecontrol.io/packages/Colorsublime) 这款插件后查找.

![](http://7xly6p.com1.z0.glb.clouddn.com/theme.gif)

# 补充

sublime 大法好，还在用其他编辑器的同学可以考虑一下了。

最后使用了这么久的sublime，这是我的插件列表，有兴趣的可以看下。

![](http://ww3.sinaimg.cn/mw690/baa3278fgw1ex3aal3t5uj206e0cct9i.jpg)

基本都是比较常用的了。
