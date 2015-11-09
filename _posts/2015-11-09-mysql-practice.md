---
layout: post
title: "[感谢燕十八老师]校招前的mysql练习集"
date:   2015-11-09 12:00
categories: 数据库
excerpt: 校招数据库 mysql 练习
---

* content
{:toc}

#Start

在这里先感谢[燕十八老师](http://weibo.com/yshiba)的 [mysql课程](http://www.zixue.it/phpvideo)，最近校招笔试面试均有涉及到一些数据库的知识，老师的这门课程很详细的讲解了mysql的基础知识，这里我把其中一些比较复杂的 sql 练习整理出来以供参考

> 相应的 DDL 语句请[点击这里](https://github.com/EasierShare/ShareDocument/blob/master/pratice.sql)

#Pratice

1.有如下表和数组

把num值处于[20,29]之间,改为20

num值处于[30,39]之间的,改为30

|| nums ||
||  3   ||
||  12  ||
||  15  ||
||  25  ||
||  23  ||
||  29  ||
||  34  ||
||  37  ||

    select floor(num/10) * 10 as num
    from nums
    where num between 20 and 39;

    答案: update nums set num = floor(num / 10) * 10
    where num between 20 and 39;

---

2.把good表中商品名为'诺基亚xxxx'的商品,改为'HTCxxxx',

提示:大胆的把列看成变量,参与运算,甚至调用函数来处理.

[substring()](http://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_substring),[concat()](http://dev.mysql.com/doc/refman/5.7/en/charset-repertoire.html)

    select goods_id, goods_name
    from goods
    where goods_name like '诺基亚%';

    select goods_id, substring(goods_name,4)
    from goods
    where goods_name like '诺基亚%';

    答案: select goods_id, concat('HTC', substring(goods_name,4))
    from goods
    where goods_name like '诺基亚%';

---

3.查询比市场价省钱200元以上的商品及该商品所省的钱(where和having分别实现)

    select goods_id,goods_name,market_price-shop_price
    from goods
    where (market_price-shop_price) > 200;

    select goods_id,goods_name,market_price-shop_price as reduce
    from goods
    having reduce > 200;

---

4.where-having-group综合练习题

有如下表及数据

|| name || subject || score ||
|| 张三 || 数学 || 90 ||
|| 张三 || 语文 || 50 ||
|| 张三 || 地理 || 40 ||
|| 李四 || 语文 || 55 ||
|| 李四 || 政治 || 45 ||
|| 王五 || 政治 || 30 ||

要求:查询出2门及2门以上不及格者的平均成绩

    错误解: select name, count(score<60) as k, avg(score)
    from stu group by name
    having k>=2;

    先查看每个人的平均成绩
    select name, avg(score) from stu group by name;

    看每个人的挂科情况
    select name, score < 60 from stu;

    计算每个人的挂科科目
    select name,sum(score < 60) from stu group by name;

    同时计算每人的平均分
    select name,sum(score < 60),avg(score) as pj from stu group by name;

    利用having筛选挂科2门以上的.
    select name,sum(score < 60) as gk ,avg(score) as pj from stu group by name having gk >=2;

---

6.查询最新商品(id 最大的)

    order by + limit:
    select * from goods order by goods_id desc limit 1

    子查询:
    select max(goods_id) from goods;
    select * from goods where goods_id = (select max(goods_id) from goods);

---

5.查询每个栏目下的最新商品(id 最大的)

    错解: select max(goods_id), goods_name, shop_price from goods group by cat_id; // 使用cat_id来分类其他栏目查询的结果为查询到的第一个结果

    错解: select max(goods_id), goods_name, shop_price from goods order by goods_id desc group by cat_id; // 语句本身错误, order by, group by 顺序颠倒

    查询每个栏目的最大id
    select cat_id, max(goods_id) from goods group by cat_id;
    查询id为这些的商品
    select * from goods where goods_id in (select max(goods_id) from goods group by cat_id);

> 字句使用顺序 where > group by > having > order by > limit

---

6.查询有商品的栏目信息

    select * from category
    where exists(
        select * from goods
        where goods.cat_id = category.cat_id);

---

7.取出所有商品的商品名,栏目名,价格

    select goods_id, cat_name, goods_name, shop_price
    from goods inner join category
    on goods.cat_id = category.cat_id;

---

8.根据给出的表结构按要求写出SQL语句

Match 赛程表

||字段名称    || 字段类型    || 描述 ||
||matchID     || int         || 主键 ||
||hostTeamID  || int         || 主队的ID ||
||guestTeamID || int         || 客队的ID ||
||matchResult || varchar(20) || 比赛结果，如（2:0） ||
||matchTime   || date        || 比赛开始时间 ||

Team 参赛队伍表

|| 字段名称  ||  字段类型    || 描述 ||
|| teamID    ||  int         || 主键 ||
|| teamName  ||  varchar(20) || 队伍名称 ||

Match的hostTeamID与guestTeamID都与Team中的teamID关联

查出 2006-6-1 到2006-7-1之间举行的所有比赛，并且用以下形式列出：

拜仁  2：0 不来梅 2006-6-21

mysql> select * from m;

|| mid || hid  || gid  || mres || matime     ||
||   1 ||    1 ||    2 || 2:0  || 2006-05-21 ||
||   2 ||    2 ||    3 || 1:2  || 2006-06-21 ||
||   3 ||    3 ||    1 || 2:5  || 2006-06-25 ||
||   4 ||    2 ||    1 || 3:2  || 2006-07-21 ||

mysql> select * from t;

|| tid  || tname    ||
||    1 || 国安     ||
||    2 || 申花     ||
||    3 || 公益联队 ||

    select hid, t1.tname, mres, gid, t2.tname as gname, matime
    from m
    inner join t as t1
    on m.hid = t1.tid
    inner join t as t2
    on m.gid = t2.tid;

---

> Union 的语句必须满足一个条件：各语句取出的列数相同，列名未必要一致，列名会使用第一条sql的列名

> 使用 union 时，完全相等的行会合并，合并比较费时，所以一般不让其合并，使用 "union all" 可以避免

> union的字句中，不用写order by，sql合并后得到的总的结果，可以order by，字句 order by 失去意义

9.根据结果写出sql语句

A表:

|| id   || num  ||
|| a    ||    5 ||
|| b    ||   10 ||
|| c    ||   15 ||
|| d    ||   10 ||

B表:

|| id   || num  ||
|| b    ||    5 ||
|| c    ||   15 ||
|| d    ||   20 ||
|| e    ||   99 ||

要求查询出以下效果:

|| id   ||    num   ||
|| a    ||        5 ||
|| b    ||       15 ||
|| c    ||       30 ||
|| d    ||       30 ||
|| e    ||       99 ||

    union + 子查询:
    select id, sum(num)
    from (select * from a union all select * from b) as tmp
    group by id;

# 结论

这些查询掌握了，对付笔试面试中的sql查询就没问题了，如果还想深入学习，可以自行查看燕十八老师的课程。
