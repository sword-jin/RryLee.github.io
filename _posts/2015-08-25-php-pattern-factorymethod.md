---
layout: post
title: "php设计模式英雄联盟篇-工厂方法"
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

在工厂方法中，你可以通过继承抽象工厂来得到工厂类，来可以实现不同的方法创建类。这也是相比之前的`静态工厂`和`简单工厂`的优点。

<img class="shadow" src="http://ww1.sinaimg.cn/mw690/baa3278fgw1eveu16wstfj20m70akq4b.jpg" />

    abstract class FactoryMethod
    {
        const RED = 1;
        const BLUE = 2;

        abstract function createHero($group);

        public function create($group)
        {
            $hero = $this->createHero($group);

            return $hero;
        }
    }

    class AnniFactory extends FactoryMethod
    {
        public function createHero($group)
        {
            switch($group) {
                case parent::RED:
                    $anni = new Anni(parent::RED);
                    $anni->choose('red');
                    return $anni;
                    break;
                case parent::BLUE:
                    new Anni(parent::BLUE);
                    $anni->choose('blue');
                    return $anni;
                    break;
                default:
                    throw new InvalidArgumentException('not exist the group');
            }
        }
    }

    class WukongFactory extends FactoryMethod
    {
        public function createHero($group)
        {
            switch($group) {
                case parent::RED:
                    return new Wukong(parent::RED);
                    break;
                case parent::BLUE:
                    return new Wukong(parent::BLUE);
                    break;
                default:
                    throw new InvalidArgumentException('not exist the group');
            }
        }
    }

    interface HeroInterface
    {
        public function getGroup();
    }

    class Anni implements HeroInterface
    {
        protected $group;

        public function __construct($group)
        {
            $this->group = $group;
        }

        public function getGroup()
        {
            switch ($this->group) {
                case 1:
                    return '红色方';
                    break;

                default:
                    return '蓝色方';
                    break;
            }
        }

        public function choose($color)
        {
            echo "I'm " . $color;
        }
    }

    class Wukong implements HeroInterface
    {
        protected $group;

        public function __construct($group)
        {
            $this->group = $group;
        }

        public function getGroup()
        {
            switch ($group) {
                case 1:
                    return '红色方';
                    break;

                default:
                    return '蓝色方';
                    break;
            }
        }
    }

未完！

> 主要看 uml 图理解就可以了，工厂模式都差不多，关键是理解其中的区别。
