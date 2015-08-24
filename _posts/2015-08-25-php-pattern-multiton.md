---
layout: post
title: "php设计模式-多例模式"
date:   2015-08-25 00:20
categories: 设计模式
excerpt: php设计模式-多例模式
---

维基百科中是这样介绍多例模式 [multiton pattern](https://en.wikipedia.org/wiki/Multiton_pattern)：

在软件工程中，多例模式是一种类似单例模式的设计模式，拓展单例能通过键值对管理一组实例化对象。

    <?php

    class Multiton
    {
        /**
         * 对象键值对
         * @type {Array}
         */
        private static $instances = [];

        /**
         * 防止实例
         */
        private function __construct() {}

        /**
         * 防止通过克隆复制对象
         */
        private function __clone() {}

        /**
         * 通过键来获取实例
         */
        public static function getInstance($name)
        {
            if (! array_key_exists($name, self::$instances)) {
                self::$instances[$name] = new self();
            }
            return self::$instances[$name];
        }
    }

---

这样一个多例就构建完成，没有好讲的，用法和单例差不多，至于用途，目前没有用到。未完