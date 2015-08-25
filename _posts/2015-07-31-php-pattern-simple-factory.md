---
layout: post
title: "php设计模式英雄联盟篇-简单工厂"
date:   2015-07-31 00:00
categories: 设计模式
excerpt: php设计模式英雄联盟篇-简单工厂
---

接下来我们来聊聊设计模式中的简单工厂，客户端实例化对象很常用的一个模式。

首先，`简单工厂，即工厂类是实体类，不是继承而来`。这点很重要，后续的工厂类还有 `工厂方法`, `抽象工厂`, `静态工厂`

在设计模式中，我们有一个重要的原则，*开闭原则*，即软件实体对拓展是开放的，但是对修改是关闭的，即在不修改一个软件实体的基础上去拓展其功能。

现在我们来假设这样一个场景，我们在一场排位赛中需要选出10个英雄，即实例化10个不同的对象，这里就假设需要2个（即solo），为了方便后面的代码。中需要选出10个英雄，即实例化10个不同的对象，这里就假设需要2个（即solo），为了方便后面的代码。

![](http://ww4.sinaimg.cn/mw690/baa3278fgw1eves9wdi63j20e30e7q3m.jpg)

---

    interface HeroInterface
    {
        public function skills();
    }
    class Anni implements HeroInterface
    {
        protected $q = '碎裂之火';
        protected $w = '焚烧';
        protected $e = '熔岩护盾';
        protected $r = '提伯斯之怒';

        public function skills()
        {
            echo "Q: " . $this->q;
            echo "W: " . $this->w;
            echo "E: " . $this->e;
            echo "R: " . $this->r;
        }
    }
    class Wukong implements HeroInterface
    {
        protected $q = '粉碎打击';
        protected $w = '真假猴王';
        protected $e = '腾云突击';
        protected $r = '大闹天空';

        public function skills()
        {
            echo "Q: " . $this->q;
            echo "W: " . $this->w;
            echo "E: " . $this->e;
            echo "R: " . $this->r;
        }
    }

我们现在有这两个英雄，或者更多的英雄，我们去实例化这些对象，我们需要这样做

    $anni = new Anni();
    $wukong = new Wukong();
    $yasuo = new Yasuo();
    ...

这些做直接暴露给客户端你有这么些个实体类，这个时候我们可以选择简单工厂将其封装起来。给我们客户端两个类，HeroFactory用来实例化对象，Hero用来创建新的英雄类。

    class HeroFactory
    {
        protected $heroList = [];

        public function __construct()
        {
            $this->heroList = [
                'anni' => 'Anni',
                'wukong' => 'Wukong',
            ];
        }

        public function createHero($name)
        {
            if (! array_key_exists($name, $this->heroList)) {
                throw new InvalidArgumentException("$name not exist");
            }
            return new $this->heroList[$name];
        }
    }

这时候，就可以这样实例化对象了:

    $factory = new HeroFactory;
    $anni = $factory->createHero('wukong');

如果你想增加一个新英雄，比如它叫 ‘敖兴(AoXing)’，我们只需要先继承我们的 Hero 类，然后在工厂类中加上，只需要在我们的 `heroList` 中添加就可以了。
