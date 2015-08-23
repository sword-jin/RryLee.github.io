---
layout: post
title: "php设计模式英雄联盟篇-原型模式"
date:   2015-08-04 00:00
categories: 设计模式
excerpt: php设计模式英雄联盟篇-原型模式
---

接下来我们来聊聊设计模式中的原型模式，它通过克隆来复制实例化的对象，以此来减少实例化对象的开销。

通常，一个项目中需要创建某个原型对象的多个实例，就可以采用原型模式，这点和单利模式恰恰相反。

现在，假设我们是英雄联盟的英雄设计师，我们需要向游戏添加新的英雄，而英雄又分为 ADC, APC, TANK 三种（假设三种，我知道还有）。我们就可以产生三个实例来赋值它们得到新的对象了。

    // HeroPrototype.php

    abstract class HeroPrototype
    {
        protected $id;
        protected $name;
        protected $picture;
        protected $price;

        // 设置价格
        public function setPrice($price)
        {
            $this->price = $price;
        }
        public function getPrice()
        {
            return $this->price;
        }

        // Id
        public function setId($id)
        {
            $this->id = $id;
        }
        public function getId()
        {
            return $this->id;
        }

        // 名字
        public function setName($name)
        {
            $this->name = $name;
        }
        public function getName()
        {
            return $this->name;
        }

        // 图片
        public function setPic($pic)
        {
            $this->picture = $pic;
        }
        public function getPic()
        {
            return $this->picture;
        }

        abstract function __clone();
    }

---

这里先做一个英雄的抽象类来定义我们这个例子的英雄结构。

    // Adc.php

    <?php

    require_once 'Prototype.php';

    class Adc extends HeroPrototype
    {
        const TYPE = 'ADC';

        function __clone(){}
    }

    // Apc.php

    include_once 'Prototype.php';

    class Apc extends HeroPrototype
    {
        const TYPE = 'APC';

        function __clone() {}
    }

    // Tank.php
    <?php

    include_once 'Prototype.php';

    class Tank extends HeroPrototype
    {
        const TYPE = 'TANK';

        function __clone() {}
    }

---

我们的三个类都已经定义好，接下来看看我们的客户端怎么去处理它们，使用原型模式的思想。

    // Client.php

    <?php

    spl_autoload_register(function ($class) {
        require $class . '.php';
    });

    class Client
    {
        private $adc;
        private $apc;
        private $tank;

        public function __construct()
        {
            $this->generatePrototype();

            $adc1 = clone $this->adc;
            $this->setPropety($adc1, '23', '女警', 'adc1.jpg', 6300);
            $this->showPropety($adc1);
            $adc2 = clone $this->adc;
            $this->setPropety($adc2, '25', '女枪', 'adc2.jpg', 3600);
            $this->showPropety($adc2);

            $tank = clone $this->tank;
            $this->setPropety($tank, '21', '牛头', 'tank.jpg', 1350);
            $this->showPropety($tank);

            $apc1 = clone $this->apc;
            $this->setPropety($apc1, '43', '皎月', 'apc1.jpg', 6300);
            $this->showPropety($apc1);
            $apc2 = clone $this->apc;
            $this->setPropety($apc2, '54', '发条', 'apc2.jpg', 4800);
            $this->showPropety($apc2);
        }

        private function generatePrototype()
        {
            $this->adc = new Adc();
            $this->apc = new Apc();
            $this->tank = new Tank();
        }

        private function setPropety(HeroPrototype $tmp, $id, $name, $picture, $price)
        {
            $tmp->setId($id);
            $tmp->setName($name);
            $tmp->setPic($picture);
            $tmp->setPrice($price);
        }

        private function showPropety(HeroPrototype $tmp)
        {
            $pic = $tmp->getPic();
            echo $tmp->getId() . "<br />";
            echo $tmp->getName() . $tmp::TYPE . "<br />";
            echo $tmp->getPrice() . "<br />";
            echo "<img src=$pic width='150' height='150'>" . "<br />";
        }
    }

    $client = new Client();

---

这里重点就在我们的构造函数中，你可以通过克隆来得到很多很多的对象。这里要记住通过 clone 方法是不会触发被克隆对象的 __construct 构造函数的。

有兴趣的同学可以去 [github](https://github.com/RryLee/PrototypePattern-Example/)上 copy 下来跑跑。未完


