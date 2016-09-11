---
layout: post
title: "[laravel] eloquent sql 查询练习"
subtitle:   ""
date:   2016-09-11
author: "RryLee"
tags:
    - qloquent
    - laravel
---

* content
{:toc}

> 准备了一下，最后决定采用更新一个项目的形式去分享一些 sql 技巧

# 准备

```bash
git clone https://github.com/RryLee/learn-eloquent-sql.git

composer install

完成代码之后
注释掉 $this->markTestSkipped();

phpunit
```

# 数据结构以及准备

```
用户 user - id, name, email
文章 post - id, title, body
评论 comment - id, user_id, post_id, comment
```

# 问题

- 获取发表过文章的用户数量
- 获取有过评论，并且评论内容前缀为 __test 的用户数量
- 获取所有文章以及它的评论数量，并且按评论数量排序

# 补充

有时间，有好的例子我都会及时更新，欢迎关注。

地址在 https://github.com/RryLee/learn-eloquent-sql
