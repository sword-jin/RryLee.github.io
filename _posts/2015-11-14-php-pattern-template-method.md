---
layout: post
title: "php设计模式-模版方法模式"
subtitle: ""
author: "RryLee"
date:   2015-11-12
tags:
    - php
    - 设计模式
---

* content
{:toc}

# 概述

对于模版方法模式[后面统称 [Template method](https://en.wikipedia.org/wiki/Template_method_pattern)]，维基是这样描述的：

> In software engineering, the template method pattern is a behavioral design pattern that defines the program skeleton of an algorithm in a method, called template method, which defers some steps to subclasses.[1] It lets one redefine certain steps of an algorithm without changing the algorithm's structure.[2] This use of "template" is unrelated to C++ templates.

一句话来讲，就是定义一个算法骨架，子类使用该模版不需要重新定义算法步骤。

Template method 在生活中也可以归纳出来很多，比如踢一场足球和打一场篮球都需要换衣服，组队，运动等一系列的步骤，工人，农民也都需要上班收工。

---

# 场景

当有两个或者多个不同的组件却有相同的解决方法的时候，这个时候可以选择 Template method，比如 laravel 中的第三方授权组件，就是采用的这种设计思想。

---

# Demo

<img class="shadow" src="http://ww3.sinaimg.cn/mw690/baa3278fgw1ey0cpxxzkgj20h10bxdgc.jpg" />

从上图可以看到农民工作和工人工作都遵循的同一个工作流程，接下来一步步写写看:

    class WorkerWork
    {
        public function work()
        {
            $this->eatBreakfast()
                ->changeClothes()
                ->gotoBuilding()
                ->goHome();
        }

        protected function eatBreakfast()
        {
            var_dump('eat breakfast');

            return $this;
        }

        protected function changeClothes()
        {
            var_dump('change the work clothes');

            return $this;
        }

        protected function gotoBuilding()
        {
            var_dump('go to building');

            return $this;
        }

        protected function goHome()
        {
            var_dump('go home');

            return $this;
        }
    }

好了，现在又出现了农民工作

    abstract class FarmerWork
    {
        public function work()
        {
            $this->eatBreakfast()
                ->changeClothes()
                ->gotoFarm()
                ->goHome();
        }

        protected function eatBreakfast()
        {
            var_dump('eat breakfast');

            return $this;
        }

        protected function changeClothes()
        {
            var_dump('change the work clothes');

            return $this;
        }

        protected function gotoFarm()
        {
            var_dump('go to farm');

            return $this;
        }

        protected function goHome()
        {
            var_dump('go home');

            return $this;
        }
    }

这个时候代码以及有一点冗余了，根据上图，很容易的想到解决方案，**使用一个抽象的工作类来定义公共的方法并且约束子类去实现特有的方法**

    abstract class GoWork
    {
        public function work()
        {
            $this->eatBreakfast()
                ->changeClothes()
                ->doOwnPrimaryTask()
                ->goHome();
        }

        protected function eatBreakfast()
        {
            var_dump('eat breakfast');

            return $this;
        }

        protected function changeClothes()
        {
            var_dump('change the work clothes');

            return $this;
        }

        protected function goHome()
        {
            var_dump('go home');

            return $this;
        }

        abstract protected function doOwnPrimaryTask();
    }

    class WorkerWork extends GoWork
    {
        protected function doOwnPrimaryTask()
        {
            var_dump('go to building');

            return $this;
        }
    }

    class FarmerWork extends GoWork
    {
        protected function doOwnPrimaryTask()
        {
            var_dump('go to farm');

            return $this;
        }
    }

    (new WorkerWork)->work();

最后，优雅的解决了问题！

---

# 总结

使用 Template method 能很好的封装功能的不变部分，同时拓展可变部分，易于维护，缺点就是相应的每个功能都需要子类来实现，导致类的个数增加。
