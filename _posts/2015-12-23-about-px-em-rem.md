---
layout: post
title: "这次我要搞清楚px,em和rem这三个单位"
date:   2015-12-23 20:40
categories: css
excerpt: css px em rem
---

* content
{:toc}

# 疑惑

相信每一个写过网页的人都曾经对这三个单位表示过一些疑惑。如果你多看一个网站的代码，不难发现国外的网站大多单位都使用 `em` 和 `rem`，相反国内则多是 `px` 了。

首先 `px`，固定的长度单位，而 `em` 则是相对与父级元素的大小来调整的，`rem` 是 `css3` 新增的一个相对单位 (root em)

---

# 简单实例

首先是默认情况下，`em` 和 `rem` 默认一个单位对应 16px

    h1 {
        font-size: 2em; /* 2 * 16 = 32px */
    }
    p {
        font-size: 2rem; /* 2 * 16 = 32px */
    }
    div {
        font-size: 32px;
    }

这个时候 h1, p, div 的字体大小均为 32px;

如果你去调整我们 `body` 节点的字体

    html {
        font-size: 62.5%; /* 10 / 16 */
    }
    h1 {
        font-size: 2em; /* 2 * 10 = 20px */
    }
    p {
        font-size: 2rem; /* 2 * 10 = 20px */
    }
    div {
        font-size: 32px;
    }

通过这两个例子，已经简单的感觉了相对大小的概念

那我们看一下 em 和 px的一些数据对比

|| *em* || *px* ||
|| html {font-size: 1em;} || html { font-size: 16px; } ||
|| h1 {font-size: 2.2em;} || h1 {font-size: 35px;} ||
|| h2 { font-size: 1.2em; } || h2 { font-size: 19px; } ||

再来看看 `rem`，只相对根节点 `html`(root) 的 `font-size` 来相对确定大小。

> 这样讲 `rem` 你大概不会体会到任何东西，那么，请看下面两个demo，你一定会有所收获

# demo1

首先简单的 html

    <h1>Look rem and em</h1>
    <div class="box">
        normal box
    </div>
    <div class="box small-box">
        small box
    </div>

盒子的 `padding` 和 `margin` 使用 em

    body {
        background: #f9f9f9;
    }
    .box {
        margin: 1.25em -1.25em 0;
        padding: 1em 1.25em;
        background-color: #cfcdc4;
    }
    .small-box {
        font-size: 0.682em;
    }

你会看到这样的画面

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1ez9wz3jlckj20o20583yl.jpg)

why? 可想而知，我们的 `small-box` 中的 font-size 影响到了 .box 的 `padding` 和 `margin`，会相对 `font-size` 来变化，这个时候你换为

    .box {
        margin: 1.25rem -1.25rem 0;
        padding: 1rem 1.25rem;
        background-color: #cfcdc4;
    }

一切正常!!!

# demo2

来看下 em 的好处，有这样一个简答的例子，你需要在手机端网页的字体比web端大 20%, 我们先使用 em 来简单的完成这个任务

    html {
        font-size: 100%;
    }
    h1 {
        font-size: 2.2em;
    }
    h2 {
        font-size: 1.8em;
    }
    h3 {
        font-size: 1.4em;
    }

    @media (max-width: 600px) {
        html {
            font-size: 120%;
        }
    }

换个思路，如果你是用的px，你的代码会增加多少呢。。。

# 总结

在以后的项目中，会尽量的来配置em和rem使用，必要的时候也是需要使用px的。
