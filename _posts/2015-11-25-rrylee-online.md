---
layout: post
title: "RryTip正式上线，欢迎访问"
date:   2015-11-21 14:00
categories: laravel
excerpt: RryTip 博客 视频 上线
---

* content
{:toc}

# About

由于一些原因，想来想去，还是自己搭建一个视频站点比较好，花了几天时间写了一个静态视频生成器。

玩过 [Github Page](https://pages.github.com/) 的同学都知道，访问响应的域名能访问到自己的静态文件，像这个博客就是基于 [jekyll](http://jekyll.bootcss.com/) 搭建的，不需要太多心思管理，专注写博客就好了。

> 所以， 基于这种想法，想做一个类似的视频站点，其实对 `jekyll` 改造下还是很容易做到的，不过，基于不想看 `jekyll` 的代码和自己闹腾的心，还是自己做了吧。

---

# Work

目前网站已经搭建在 github 上了，[hello world](http://rrytip.github.io/home/about-easier/hello-world.html) 这个视频介绍了本应用的一些使用。

这里讲下思路吧，和 `jekyll` 一样配置文件生成相应的页面，然后一般动态生成的页面只需要有[首页, 显示列表]，[视频页，显示视频和列表]。

一些服务，比如视频服务，一开始想使用 [vimeo](https://vimeo.com/) 的，后来发现两个问题

* 国内速度问题(都懂的)

* 账户不升级不支持引用播放 HD

所以，还是选择国内的一些视频服务好一点，就`优酷`来说，除了广告有点长，其他还是不错的，广告的话装个 [ADSafe](http://www.ad-safe.com/?b1) 还是很方便的。

然后就是评论服务，个人比较喜欢 [Disqus](https://disqus.com/) 的，界面更加清新，国内支持还是不错的，另外的话[多说](http://duoshuo.com/)这个评论系统也是可以的。

总之，一些都来自配置文件，基本都是文件操作。

---

# 总结

目前来说，这个系统就我自己使用还是不错的，如果有同学也想使用，以后或许会开源的，只要代码不是太烂。最后，上一张图！

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1eyd44wlvy0j20wl0jjtav.jpg)
