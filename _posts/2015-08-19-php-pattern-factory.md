---
layout: post
title: "php设计模式英雄联盟篇-工厂方法"
date:   2015-08-19 16:30
categories: 设计模式
excerpt: php设计模式英雄联盟篇-工厂方法
---

前面的[简单工厂](http://rrylee.github.io/2015/07/31/php-pattern-simple-factory/)中有了解了如何通过工厂的方法统一的创建类，当业务加强时，我们就需要改进我们的简单工厂。

前面我们创建新的类之后，需要在我们的 `HeroFactory` 中去增添代码判断，这种做法很不好，当业务变大之后会逐渐变得难以维护。这个时候需要引入另一个接口(我们之前的 HeroFactory 类就被抛弃了)。

    // HeroFactory
    interface HeroFactory
    {
        function createHero();
    }

---

我们的具体英雄工厂可以通过继承这个借口来得到拓展，现在我们可以这样来得到我们的对象：

    class AnniFactory implements HeroFactory
    {
        function createHero()
        {
            return new Anni;
        }
    }

    $annifactory = new AnniFactory();
    $anni = $annifactory->createHero();
    $anni->skills();

---

这样我们有新的类出现时，就不在需要去修改原来的 `HeroFactory` 代码。

>总结：当然，工厂方法也不可能这么简单，但是我们需要明白一个重要的思想，就是新建类时一定不能修改我们原始的接口方法。
