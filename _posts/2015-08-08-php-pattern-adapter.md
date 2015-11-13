---
layout: post
title: "php设计模式英雄联盟篇-适配器模式"
date:   2015-08-08 00:00
categories: 设计模式
excerpt: php设计模式英雄联盟篇-适配器模式
---

* content
{:toc}

# 概念

接下来是我们的适配器模式，这个模式比较简单，理解也比较容易，看下面的例图(借图)：

![Adapter](http://ww3.sinaimg.cn/mw690/baa3278fgw1exz3sdff7pj20hb0aj75t.jpg)

---

# 例子

什么时候使用适配器模式，这个问题和图中反应出来的差不多，你需要使用适配器就需要使用适配器模式。说实话，这个 lol 的例子我想了很久也没想出比较恰当的，下面扯一个牵强一点的例子来说明这个问题，当然，也会用到我们 lol 中的一些人物。

下面假设我们需要做一个英雄信息的接口来给客户端使用，首先，我们的借口是这个样子的：

    // HeroInfo.php
    <?php
    class HeroInfo
    {
        protected $name;
        protected $price;
        protected $type;
        protected $info;

        public function __construct($name, $price, $type)
        {
            $this->name = $name;
            $this->price = $price;
            $this->type = $type;
            $this->info = ['name' => $this->name, 'price' => $this->price, 'type' => $this->type];
        }

        protected function store()
        {
            return serialize($this->info);
        }

        public function show()
        {
            $info = unserialize($this->store());
            echo "名字: " . $info['name'] . "<br />";
            echo "价格: " . $info['price'] . "<br />";
            echo "类型: " . $info['type'] . "<br />";
        }
    }

    $anni = new HeroInfo('Anni', '3150', 'AP');
    $anni->show();

---

我们现在可以很好的是调用这个方法来输出，假设有一部分合作方它们使用 java 来开发， java 可没有 serialize 和 unserialize 这些函数，那么这个时候，我们需要一个适配器来耦合不同的语言。可想而知，json/xml 这个标记语言是通用的。那么我们可以这样：

    // HeroInfo.php
    class AdapterHeroInfo extends HeroInfo
    {
        protected function store()
        {
            return json_encode($this->info);
        }

        public function show()
        {
            return json_decode($this->store());
        }
    }
    $Wukong = new AdapterHeroInfo('Wukong', '6300', 'Tank');
    $Wukonginfo = $Wukong->show();
    echo "名字: " . $Wukonginfo->name . "<br />";
    echo "价格: " . $Wukonginfo->price . "<br />";
    echo "类型: " . $Wukonginfo->type . "<br />";

---

这样的话，我们的 java/js 端拿到这个数据也就能解析了。这是一个很牵强的例子，但足以说明，在某些场合下我们需要这样一个适配器来耦合我们的业务需求。

# 补充

后来回过头来看了一下，上面的例子真的不是很好，来举个 github 上关于设计模式的例子。

首先用户去阅读一本书，这里就有两个类，用户类和书籍类

    interface BookInterface
    {
        public function open();
        public function turnPage();
    }

    class Book implements BookInterface
    {
        public function open()
        {
            var_dump('open the book.');
        }

        public function turnPage()
        {
            var_dump('turn the page on the book.');
        }
    }

    class User
    {
        public function read(BookInterface $book)
        {
            $book->open();
            $book->turnPage();
        }
    }

在客户端你可以这样使用 `(new User)->read()`

之后又开始使用 EBook 来阅读

    interface EBookInterface
    {
        public function turnOn();
        public function clickNext();
    }

    class EBook implements EBookInterface
    {
        public function turnOn()
        {
            var_dump('turn on the EBook');
        }

        public function clickNext()
        {
            var_dump('click the next button on the EBook');
        }
    }

但 EBook 没有 open 和 turnPage 这样的方法，这个时候使用适配器就是再好不过的了。

    class EBookAdapter implements BookInterface
    {
        protected $EBook;

        public function __construct(EBookInterface $EBook)
        {
            $this->EBook = $EBook;
        }

        public function open()
        {
            return $this->EBook->turnOn();
        }

        public function turnPage()
        {
            return $this->EBook->clickNext();
        }
    }

最后在客户端只需这样使用 `(new User)->read(new EBookInterface(new EBook))`
