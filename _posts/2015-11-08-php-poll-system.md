---
layout: post
title: "投票系统原型参考"
date:   2015-11-08
subtitle: ""
author: "RryLee"
tags:
    - php
---

* content
{:toc}

# 概要
---

看到各种微博，微信投票小应用，复杂点的类似[牛客](http://www.nowcoder.com/)那种答题系统，其实，就功能而言，实现起来并不难，下面给出系统的简单设计和一些难点细节。

# 数据库设计
---

<img class="shadow" src="http://ww4.sinaimg.cn/mw690/baa3278fgw1extdu4rjd5j20d40bo3z1.jpg" />

---

就上图而言，我们的数据库设计已经成型了，有用户，投票问题，投票选项，投票答案。
具体可以参考 [db](https://coding.net/u/RryLee/p/PHP-Poll-System/git/blob/master/db.sql)

# 一些细节
---

#### 显示有效投票问题列表

在 polls 表中我们通过设置开始时间(start)和结束时间(end)来控制投票，具体sql为

    SELECT id, question FROM polls WHERE DATE(now()) BETWEEN start AND end

#### 防止重复投票

该投票系统理论上需要用户登录才能进行，所以一定程度上能防止刷票，接下来，如何防止用户重复投票，以及投票是否合法，需要做到三点：

* poll 是存在切有效的(该poll没有过期).

* choice 和 poll 存在且对应(在poll_choices表中有对应的选项和问题)

* 该用户没有投过该问题的票(即poll_answers表中没有对应的用户和问题)

具体sql为

    INSERT INTO poll_answers (user_id, poll_id, choice_id)
        SELECT :user_id, :poll_id, :choice_id
        FROM polls
        WHERE EXISTS (
            SELECT id
            FROM polls
            WHERE id = :poll_id
            AND DATE(NOW()) BETWEEN polls.start AND polls.end)
        AND EXISTS (
            SELECT id
            FROM poll_choices
            WHERE id = :choice_id
            AND poll_id = :poll_id)
        AND NOT EXISTS (
            SELECT id
            FROM poll_answers
            WHERE user_id = :user_id
            AND poll_id = :poll_id)
        LIMIT 1)

这里使用预处理语句来防止sql注入，`user_id`, `poll_id`, `choice_id` 依次为用户id，投票问题id，选项id

#### 显示投票百分比

这里我们需要求出每个问题每个选项的投票百分比，关键还是两个count和使用group by来分割每个选项结果，具体sql为

    SELECT poll_choices.title,
    COUNT(poll_answers.id) * 100 / (
        SELECT COUNT(*)
        FROM poll_answers
        WHERE poll_answers.poll_id = :poll_id) AS percentage
    FROM poll_choices
    LEFT JOIN poll_answers
    ON poll_choices.id = poll_answers.choice_id
    WHERE poll_choices.poll_id = :poll_id
    GROUP BY poll_choices.id)

# 结论

其实一个投票系统的核心就是这些，剩下的就是怎么去优化了，具体代码可以参考[PHP-Poll-System](https://coding.net/u/RryLee/p/PHP-Poll-System/git)，纯面向过程，no mvc。

# 拓展

当投票数据拿到手之后，不一定需要求百分比，你可以做各种处理，又或是你想做一个答题系统，需要得到最后分数，你在polls表中增加一个正确选项的chioce_id即可。
