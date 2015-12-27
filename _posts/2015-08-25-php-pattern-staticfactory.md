---
layout: post
title: "php设计模式英雄联盟篇-静态工厂"
date:   2015-08-25
subtitle: ""
author: "RryLee"
tags:
    - php
    - 设计模式
---

* [工厂模式---简单工厂](http://rrylee.github.io/2015/07/31/php-pattern-simple-factory/)
* [工厂模式---工厂方法](http://rrylee.github.io/2015/08/25/php-pattern-factorymethod/)
* [工厂模式---静态工厂](http://rrylee.github.io/2015/08/25/php-pattern-staticfactory/)
* [工厂模式---抽象工厂](http://rrylee.github.io/2015/08/25/php-pattern-abstructfactory/)

静态工厂和之前的简单工厂有些类似，两者的区别在于`静态工厂中有一个静态方法用来实例化它所能实例化的所有对象`

<img class="shadow" src="http://ww1.sinaimg.cn/mw690/baa3278fgw1evesrm3v3fj20dd0by3z9.jpg" />

从图中可以看出和简单工厂确实比较类似，只需要修改我们的工厂实体类

    class StaticFactory
    {
        public static function createHero($name)
        {
            $className = ucfirst($name);

            if (! class_exists($className)) {
                throw new InvalidArgumentException("$className not exist.");
            }

            return new $className;
        }
    }

4个工厂类中的两个实体工厂类就告一段落了，未完.
