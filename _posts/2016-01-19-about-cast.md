---
layout: post
title: "Eloquent 使用 casts 来处理数据 -- 进阶"
subtitle:   ""
date:   2016-01-19
author: "RryLee"
tags:
    - php
    - Eloquent
---

* content
{:toc}

＃概述

有很多时候你会想去使用json来存储数据，那么，使用 $casts 是最好不过的选择，但是不仅如此，你还可以做的更多。

# Basic

这里是官方文档， [link](http://www.golaravel.com/laravel/docs/5.1/eloquent-mutators/#attribute-casting).

还有 [RryTip](http://rrytip.github.io) 的一个关于 `casts` 的视频， [Use casts change your datetype ](http://rrytip.github.io/home/laravel-tips/use-casts-change-your-datetype.html)

# 加深

那么，我们来更进一步的去处理这种数据关系。

这里有一个用户图像(avatar)的例子，我们需要存储上传的原图(original)，以及处理后的小图(thumbnail)，你可以把这里看成一个 `one-to-one` 的关系模型，自然的就有了我们的 Avatar 这个类。

# Demo

简单起见，修改 `create_users_table`

    $table->increments('id');
    $table->string('name');
    $table->json('avatar')->nullable();
    $table->timestamps();

然后去 `User.php` 中定义一下

    protected $fillable = [
        'name', 'avatar'
    ];

    protected $casts = [
        'avatar' => 'json'
    ];

现在简单的定义好了，还没有进入后面的，不过先去测试一下, `routes.php`

    Route::get('/', function() {
        User::create([
            'name' => 'RryLee',
            'avatar' => [
                'original' => 'original-avatar.jpg',
                'thumbnail' => 'original-avatar.thumbnail.jpg',
            ]
        ]);
    });

    Route::get('/get', function() {
        $user = User::findOrFail(1);

        return $user;
    });

不用看，数据是肯定能存储的，也能获取到。

# 问题来了

你现在怎么去修改(这里只是假如，用户又重新剪裁了原图，只是说明修改这种情况)，这个时候还真的需要引入我们的 `Avatar`　类了.

    <?php

    namespace App;

    use App\User;
    use Exception;

    class Avatar
    {
        protected $user;

        protected static $allowed = [
            'original', 'thumbnail'
        ];

        protected $avatar = [];

        public function __construct($avatar, User $user)
        {
            $this->avatar = $avatar;
            $this->user = $user;
        }

        public function get($key)
        {
            return array_get($this->avatar, $key);
        }

        public function set($key, $value)
        {
            $this->avatar[$key] = $value;

            return $this->persist();
        }

        public function has($key)
        {
            return array_key_exists($key, $this->avatar);
        }

        public function all()
        {
            return $this->avatar;
        }

        public function merge(array $attributes)
        {
            $this->avatar = array_merge(
                $this->avatar,
                array_only($attributes, static::$allowed)
            );

            return $this->persist();
        }

        protected function persist()
        {
            return $this->user->update([
                'avatar' => $this->avatar
            ]);
        }

        public function __get($key)
        {
            if ($this->has($key)) {
                return $this->get($key);
            }

            throw new Exception("The {$key} avatar not exists.");
        }
    }

> 这里我直接拿来了一段代码，　注意 `persist()`

接着像添加一个关系一样去 `User` 模型中添加一下就可以了.

    public function avatar()
        {
        return new Avatar(isset($this->avatar) ? $this->avatar : [], $this);
    }

现在就可以尽情的对我们的 `avatar` 来进行操作了

    Route::get('save', function() {
        $user = User::findOrFail(1);
        $avatar = $user->avatar();

        $avatar->set('original', 'change.jpg');

        return 'Done!';
    });

数据库中的值应该变化了!

# 总结

`Eloquent` 真的各种提高效率，各种简单，各种变化！
