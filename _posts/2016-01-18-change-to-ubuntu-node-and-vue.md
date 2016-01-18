---
layout: post
title: "[笔记]Ubuntu下安装node开发vue应用"
subtitle:   ""
date:   2016-01-18
author: "RryLee"
tags:
    - 环境
---

* content
{:toc}

# 故事

最近由于种种原因，突然爱上了 `Ubuntu`，本来是计划换 `mac` 的，现在看来貌似没有这个需求了，相反 `linux` 能更好的工作。php 方面的工作基本转移成功，现在就是 node 这边的一些笔记了。

# 安装 node

首先一般我们会直接 `apt-get install nodejs` 来安装，然后再怎么怎么样，这里就坑了，你会得到一个很低的版本。

有几种安装方式，去官网下载源码安装，或者使用编译后的包。这里我推荐使用一个很好的 [ppa](相当于第三方包)。

[https://github.com/nodesource/distributions](https://github.com/nodesource/distributions) 点击进去你就知道怎么安装了，当前版本 5.4.1 Stable，和官方同步。

安装好之后就是 `node -v` `npm -v` 来查看了，基本没错。

# 安装 vue-cli

随着 `vue` 越来越火，`vue` 的官方工具也越来越多。这里去直接安装 `vue-cli`。

`apt-get install -g vue-cli`

安装完成之后就可以使用 `vue` 的命令了。

# Demo

这里感觉和 `laravel` 很像了，直接 `vue init webpack-simple vue-learn` 这样就建好了一个名为 `vue-learn` 的 webpack 项目，当然还有其他选择，可以参考文档。

`npm dev`

启动成功！

<img src="/img/posts/vue-result.png" class="shadow">

# 秀一下我的 Ubuntu

<img src="/img/posts/ubuntu-desktop.png" class="shadow">

> 颜值这么高，win的朋友还不换吗! 以后 [RryTip](http://rrytip.github.io) 的视频将换到 ubuntu 下录制
