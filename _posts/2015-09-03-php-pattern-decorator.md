---
layout: post
title: "php设计模式英雄联盟篇-装饰器模式"
date:   2015-09-03 20:55
categories: 设计模式
excerpt: php设计模式英雄联盟篇-装饰器模式
---

* content
{:toc}

## 概述

首先，你想给一个对象增加行为，可以使用继承来实现，但是这种方法是静态的，用户不能控制增加行为的方式和时机。这时候引出装饰器模式。

## 优势

动态地给一个对象添加一些额外的职责或者行为。就增加功能来说， Decorator模式相比生成子类更为灵活。

## 实例

这里有一个很好的英雄伤害计算的例子：

    class BasicAttack
    {
        public function getDamage()
        {
            return 65;
        }
    }

这个时候若是我们的英雄购买一把大件，攻击增加了，你或许会这样来做

    class AttackWithSword
    {
        public function getDamage()
        {
            return 65 + 40;
        }
    }

之后又购买了暴击装备，我们的攻击有可能暴击，可以这样做

    class DoubleAttackWithSword
    {
        public function getDamage()
        {
            return (65 + 40) * 2;
        }
    }

那么，问题来了，如果英雄没买大件，先裸了一把红叉(加暴击)，你会怎么做？再来一个新的类：

    class DoubleAttack {}

装备增加，我们又要增这样不停的增加类。这时候我们构造一个接口：

    interface AttackInterface
    {
        public function getDamage();
    }

我们的每个类去 implements 这个接口就可以了，或许可以这样来使用：

    class BasicAttack implements AttackInterface
    {
        public function getDamage()
        {
            return 65;
        }
    }

    class AttackWithSword implements AttackInterface
    {
        protected $attackService;

        public function __construct(AttackInterface $attackService)
        {
            $this->attackService = $attackService;
        }

        public function getDamage()
        {
            return 40 + $this->attackService->getDamage();
        }
    }

    class DoubleAttack implements AttackInterface
    {
        protected $attackService;

        public function __construct(AttackInterface $attackService)
        {
            $this->attackService = $attackService;
        }

        public function getDamage()
        {
            return $this->attackService->getDamage() * 2;
        }
    }

    echo (new DoubleAttack((new AttackWithSword(new BasicAttack))))->getDamage();
    echo (new DoubleAttack(new BasicAttack))->getDamage();

这样很轻松的得到了有装备和没有装备的双倍攻击伤害。

## 总结
不得不说，装饰器模式在给对象增加新的特性时非常的灵活，就以上的例子，你还可以不停的迭代。未完！
