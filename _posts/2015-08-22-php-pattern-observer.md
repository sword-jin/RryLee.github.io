---
layout: post
title: "php设计模式-观察者模式"
date:   2015-08-22
subtitle: ""
author: "RryLee"
tags:
    - php
    - 设计模式
---

观察者模式是为了让一个对象观察某个状态，如果发生改变，则通知所有的订阅对象。*核心在于不需要多个对象去观察给定的状态*，例如最常见的博客订阅了，就是这种思想。

利用 php 内置的 SplObserver, SplSubject 和 SplObjectStorage 这三个接口能轻松的构建 PHP 观察者模式。

这里对 Spl 还不是很了解的，推荐慕课网的[站在巨人的肩膀上写代码—SPL](http://www.imooc.com/learn/150)这步课程学习学习。

先看下官网这三个接口的描述

    SplSubject {
        abstract public void attach ( SplObserver $observer )
        abstract public void detach ( SplObserver $observer )
        abstract public void notify ( void )
    }

    SplObserver {
        abstract public void update ( SplSubject $subject )
    }

    SplObjectStorage implements Countable , Iterator , Serializable , ArrayAccess {
        ... //很多
    }

---

SplObjectStorage 这个接口来帮助储存我们的订阅对象，下面看 demo 来理解：

    class DemoSubject implements SplSubject
    {
        private $observers;
        private $foo;

        public function setObservers()
        {
            $this->observers = new SplObjectStorage();
        }

        public function attach(SplObserver $observer)
        {
            $this->observers->attach($observer);
        }

        public function detach(SplObserver $observer)
        {
            $this->observers->detach($observer);
        }

        public function notify()
        {
            foreach ($this->observers as $observer)
            {
                $observer->update($this);
            }
        }

        public function set($foo)
        {
            $this->foo = $foo;
        }

        public function get()
        {
            return $this->foo;
        }
    }

    class DemoObserver implements SplObserver
    {
        public function update(SplSubject $subject)
        {
            echo $subject->get() . "<br />";
        }
    }

    class Client
    {
        public function __construct()
        {
            echo "<p>三个观察者，一个主题</p>";
            $ob1 = new DemoObserver();
            $ob2 = new DemoObserver();
            $ob3 = new DemoObserver();

            $subject = new DemoSubject();
            $subject->setObservers();
            $subject->set('bar!');
            $subject->attach($ob1);
            $subject->attach($ob2);
            $subject->attach($ob3);

            $subject->notify();

            echo "<p>取消第三个</p>";
            $subject->detach($ob3);
            $subject->notify();

            echo "<p>添加第三个，取消第二个</p>";
            $subject->attach($ob3);
            $subject->detach($ob2);
            $subject->notify();
        }
    }

    (new Client);

代码有点多，但是很简单，输出如下：

    三个观察者，一个主题

    bar!
    bar!
    bar!
    取消第三个

    bar!
    bar!
    添加第三个，取消第二个

    bar!
    bar!

---

但是，如果不使用 spl 接口，该怎么实现呢，这时候需要定义我们自己的抽象类

    abstract class MySubject
    {
        protected $data; // 订阅数据
        protected $observers = []; // 订阅者数组，即不使用我们之前的 SplObjectStorage 接口

        public function attachObserver(MyObserver $observer)
        {
            array_push($this->observers, $observer);
        }

        public function detachObserver(MyObserver $obs)
        {
            $this->observers = array_filter($this->observers, function($observer) use ($obs) {
                return $observer != $obs;
            });
        }

        public function notify()
        {
            foreach ($this->observers as $observer)
            {
                $observer->update($this);
            }
        }
    }

    class BlogSubject extends MySubject
    {
        public function set($foo)
        {
            $this->data = $foo;
            $this->notify();
        }

        public function get()
        {
            return $this->data;
        }
    }

    interface MyObserver
    {
        public function update(MySubject $subject);
    }

    class ChinaObserver implements MyObserver
    {
        private $data;

        public function update(MySubject $subject)
        {
            $this->data = $subject->get();
            echo "在中国: " . $this->data . "<br />";
        }
    }

    class USAObserver implements MyObserver
    {
        private $data;

        public function update(MySubject $subject)
        {
            $this->data = $subject->get();
            echo "在美国: " . $this->data . "<br />";
        }
    }

    class Client
    {
        public function __construct()
        {
            $subject = new BlogSubject();
            $subject->set('观察者模式');

            $ob1 = new ChinaObserver();
            $ob2 = new USAObserver();

            $subject->attachObserver($ob1);
            $subject->attachObserver($ob2);
            $subject->notify();
        }
    }
    (new Client);

---

总结：PHP 在底层用繁琐的数据结构封装了一系列的 spl 函数，通过其中的三个接口就能很轻松的构建观察者模式，但是，没有这些，想要完成也是可以的。在一些 CMS 应用中，观察者模式随处可见，未完!
