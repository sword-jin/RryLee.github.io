---
layout: post
title: "[Tips]在自己的项目中自由的使用Eloquent"
date:   2015-10-24
subtitle: ""
author: "RryLee"
tags:
    - php
    - eloquent
---

* content
{:toc}

# 目的

相信laravel爱好者在一些非laravel项目中都会感觉数据库操作这块比较费力吧，就我而言，Eloquent是我在数据库操作这块见过最好的包了，优雅安全的操作让人爱不释手。

那么在其他项目该怎样使用Eloquent呢，下面来做一个介绍。

# 准备

使用 composer 包管理工具来继承我们的 Eloquent，其实[文档](https://github.com/illuminate/database)以及给出来一部分介绍了，下面给出一种实践方法。

<img class="shadow" src="http://ww4.sinaimg.cn/mw690/baa3278fgw1exc0w6uiuaj20td0g7q4r.jpg" />

现在去 config/database.php 中配置我们的数据库连接信息，这里可以参考文档

    use Illuminate\Database\Capsule\Manager as Capsule;

    $capsule = new Capsule;

    $capsule->addConnection([
        'driver'    => 'sqlite',
        'database'  => __DIR__ . '/../database/db.sqlite',
    ]);

    $capsule->setAsGlobal();
    $capsule->bootEloquent();

这里是最简单的配置了，具体还有很多参数，请看文档，都很简单。

# Demo

你可以选择一个文件加来专门管理数据库文件，比如上面的database这个文件夹，使用sqlite的可以先创建db.sqlite这个文件。

加入现在需要新建 users 表，可以选择建立 create_users_table.php 这个文件

    use Illuminate\Database\Capsule\Manager as Capsule;

    Capsule::schema()->create('users', function($table)
    {
        $table->increments('id');
        $table->string('email')->unique();
        $table->timestamps();
    });

然后新建我们的入口文件 .php

    require 'vendor/autoload.php';
    require 'config/database.php';
    require 'database/create_users_table.php';

像有 IDE 的同学直接运行就完成了，Sublime 配置好之后也能直接运行。下面的finished代表着表新建成功了，然，你不一定需要把表放在这个文件来简历，可以用一个专门的文件，这里只是一个演示。最终还是说明 laravel 的 migrate 多么方便。

<img class="shadow" src="http://ww4.sinaimg.cn/mw690/baa3278fgw1exc1tr87skj20pt0bwjsc.jpg" />

下面可以建立一个 User 模型了：

    namespace Acme\Models;

    use Illuminate\Database\Eloquent\Model as Eloquent;

    class User extends Eloquent
    {
        protected $fillable = ['email'];
    }

接下来的使用就和laravel没什么区别了。

<img class="shadow" src="http://ww3.sinaimg.cn/mw690/baa3278fjw9exc22dd79jj20n60gtmza.jpg" />

# 总结

真的laravel是在是太方便了，但是当不能使用laravel的时候，也要尽量来使用Eloquent来简化你的工作。

# Optianal

可能你会想用到模型的事件机制，那么你需要 `composer require illuminate/events`

接下来配置下数据库，可参考文档：

    use Illuminate\Events\Dispatcher;
    use Illuminate\Container\Container;
    $capsule->setEventDispatcher(new Dispatcher(new Container));

来测试一下，监听 User 模型的 saving 事件，在 User.php 中添加：

    public static function boot()
    {
        parent::boot();

        self::saving(function($user) {
            print "Updating" . PHP_EOL;
        });
    }

<img class="shadow" src="http://ww1.sinaimg.cn/mw690/baa3278fgw1exc2a98czsj20kq0flmyh.jpg" />
