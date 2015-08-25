---
layout: post
title: "php设计模式英雄联盟篇-抽象工厂"
date:   2015-08-25 14:50
categories: 设计模式
excerpt: php设计模式英雄联盟篇-抽象工厂
---

* [工厂模式---简单工厂](http://rrylee.github.io/2015/07/31/php-pattern-simple-factory/)
* [工厂模式---工厂方法](http://rrylee.github.io/2015/08/25/php-pattern-factorymethod/)
* [工厂模式---静态工厂](http://rrylee.github.io/2015/08/25/php-pattern-staticfactory/)
* [工厂模式---抽象工厂](http://rrylee.github.io/2015/08/25/php-pattern-abstructfactory/)

抽象工厂的定义：

Provide an interface for creating families of related or dependent objects without specifying their concrete classes。 为创建一组相关或相互依赖的对象提供一个接口，而且无需指定它们的具体类。

抽象工厂模式是工厂方法模式的升级版本，在有多个业务品种、业务分类时，通过抽象工厂模式产生需要的对象是一种非常好的解决方式。

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1evev3fi9smj20dd0hv3zy.jpg)

    interface HeroInterface
    {
        public function getAttackWay();

        public function viability();

        public function getName();
    }

    abstract class AbstractADCHero implements HeroInterface
    {
        public function getAttackWay()
        {
            echo "ADC是远程攻击" . "<br />";
        }

        public function viability()
        {
            echo "ADC生存能力弱" . "<br />";
        }
    }

    abstract class AbstractTankHero implements HeroInterface
    {
        public function getAttackWay()
        {
            echo "坦克是近战攻击" . "<br />";
        }

        public function viability()
        {
            echo "坦克生存能力强" . "<br />";
        }
    }

    class Kaitlyn extends AbstractADCHero
    {
        public function getName()
        {
            echo "女警：凯特琳" . "<br />";
        }
    }

    class Moffitt extends AbstractTankHero
    {
        public function getName()
        {
            echo "石头人：莫菲特" . "<br />";
        }
    }

    abstract class HeroFactory
    {
        abstract public function createHero($name);
    }

    class ADCFactory extends HeroFactory
    {
        public function createHero($name)
        {
            return new $name();
        }
    }

    class TankFactory extends HeroFactory
    {
        public function createHero($name)
        {
            return new $name();
        }
    }

---

关键点在于抽象方法 implements 我们的共同接口后还可以添加自己的方法，也可许选择在具体工厂中去实现，很灵活。

四种工厂模式都是设计模式中常用的创建型模式，完.
