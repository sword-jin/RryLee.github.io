---
layout: post
title: "php安装redis拓展以及laravel5中redis缓存的使用"
feature-img: "img/redis-1.png"
---
#### 记录下redis的学习使用，方便以后换了平台之后查询，已经分享下使用过程

**首先，PHP安装redis扩展，百度谷歌的好多解决方案已经老了，这里详细记录下windows的安装过程，因为linux下编译安装拓展是很方便的。**

看下自己php版本信息里面的这几个环境参数，拓展分好多不同版本编译的，但是寻找自己对应的拓展下载

![file](http://i.niupic.com/images/2015/07/28/55b670cc6e2bc.jpg)

* [igbinary 拓展](http://windows.php.net/downloads/pecl/releases/igbinary/1.2.1/)
* [redis 拓展](http://windows.php.net/downloads/pecl/snaps/redis/2.2.5/)

下载好这两个拓展之后把两个 .dll 复制到 .../php/ext 目录下，然后讲拓展添加到 php.ini中

    ;php_redis
    extension=php_igbinary.dll
    extension=php_redis.dll

重启apache就能在 `php_info中` 看到 `redis` 的拓展信息了

接着安装 [redis](http://redis.io/download)，windows就去github上下载编译好的redis包吧，[地址](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip)，解压随便放在一个目录下就可以启动redis服务了，在目录下运行

    redis-server.exe redis.conf

这时候任务管理器中就能看到 redis-server.exe启动了，接着测试下我们是否成功安装，继续执行

    redis-cli

启动 `redis` 命令行客户端(或许不叫这个名称，不清楚)

![file](https://dn-phphub.qbox.me/uploads/images/201505/29/1202/FASWZmjK3E.jpg)

这里只是测试，redis的更多数据结构和操作查看文档

下面就可以去我们的laravel中使用redis缓存了，安装 predis/redis

    "predis/predis": "*"

接下来修改我们的配置文件 chche.php

    'default' => 'redis'

redis的一些配置在database.php在中可以看到

    'redis' => [

        'cluster' => false,

        'default' => [
            'host'     => '127.0.0.1',
            'port'     => 6379,
            'database' => 0,
        ],

    ],

这里使用默认的就可以了，接着去测试一下使用情况

    Route::get('test', function(){
        if (\Cache::has('test')) {
            echo '存在chche,读取'.'<br />';
            echo \Cache::get('test');
        } else{
            echo '不存在cache,现在创建'.'<br />';
            $time = \Carbon\Carbon::now()->addMinutes(10);
            $redis = \Cache::add('test', '我是缓存资源', $time);
            echo \Cache::get('test');
        }
    }

第一次进去 http:localhots:8000/test 地址，会看到

    不存在cache,现在创建
    我是缓存资源

这时候可以去我们的客户端中查询下当前redis使用情况

    >redis 127.0.0.1:6379> keys *
    >1) "laravel:test"
    >redis 127.0.0.1:6379> ttl laravel:test
    >(integer) 590
    >redis 127.0.0.1:6379> ttl laravel:test
    >(integer) 586

你也可以使用其他缓存方法，laravel文档中也有详细说明

这里只是演示了下，有点注意的地方，laravel在使用redis储存缓存时貌似会序列化value值，所以你的静态资源缓冲时需要注意，至于怎么写最好，还请社区高人指点下。

想想，使用redis减轻数据库的压力，还可以做很多有趣的事情！
