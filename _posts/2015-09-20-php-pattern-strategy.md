---
layout: post
title: "php设计模式-策略模式"
date:   2015-09-20
subtitle: ""
author: "RryLee"
tags:
    - php
    - 设计模式
---

* content
{:toc}

# 概述

策略模式是设计模式中和工厂模式比较相似的一种，区别在于(个人认为工厂模式返回的是具体对象，而策略模式则返回这个对象的方法结果).

# 用途

一般来说，当一个 `action` 有多种实现方法时，客户端在使用时，需要根据不同的情况选择不同的方法来执行 `action`，这个时候考虑使用策略模式。

# 实例

这里有一个简单计算器功能的实现，里面有一个重要的动作 `calculate` ，可以将它抽象成一个接口，后面的计算功能来实现它。

<img class="shadow" src="http://ww3.sinaimg.cn/mw690/baa3278fgw1ew8r8dvmyzj20jo0a6aa5.jpg" />

    interface Formula
    {
        public function calculate($a, $b);
    }

    class Addition implements Formula
    {
        public function calculate($a, $b)
        {
            return $a + $b;
        }
    }

    class Subtraction implements Formula
    {
        public function calculate($a, $b)
        {
            return $a - $b;
        }
    }

    class Multiplication implements Formula
    {
        public function calculate($a, $b)
        {
            return $a * $b;
        }
    }

    class Division implements Formula
    {
        public function calculate($a, $b)
        {
            return $a / $b;
        }
    }

    class Calculator
    {
        protected $calculator;

        public function __construct(Formula $type)
        {
            $this->calculator = $type;
        }

        public function getResult($a, $b)
        {
            return $this->calculator->calculate($a, $b);
        }
    }

这样就将一个虚拟计算器封装好了，只需要像这样调用就可以得到结果。

    $add = new Calculator(new Addition);
    print $add->getResult(12, 3);

# 总结

在设计模式中有一个重要的原则，组合大于继承，在 `Calculator` 中将我们的各个类组合起来形成计算器。
