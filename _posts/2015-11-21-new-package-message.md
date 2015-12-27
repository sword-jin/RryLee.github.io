---
layout: post
title: "package for laravel-新的消息提醒包-依赖于jQuery的toastr"
subtitle: ""
author: "RryLee"
date:   2015-11-21
tags:
    - package
    - laravel
---

* content
{:toc}

> 对使用bootstrap的alert提醒框用腻的同学可以使用 toastr，很轻量，[地址](https://github.com/RryLee/laravel-reminder)

# laravel-reminder

Inspired by [laracasts flash](https://github.com/laracasts/flash)

### Install

Using Composer

    composer require rry/reminder

Add the service provider to `config/app.php`

    Rry\Reminder\ReminderServiceProvider::class,

Optionally include the Facade in config/app.php if you'd like.

    'Reminder'  => Rry\Reminder\ReminderFacade::class,

> You can use reminder() function available.

### Dependencies

jQuery [toast](https://github.com/CodeSeven/toastr), you need to add css and js to your html.

### Basic

You should add `{!! Reminder::message() !!}` to your html.

Then.

    Reminder::info('foo', 'bar', []);

    Reminder::success('foo', 'bar', []);

    Reminder::warning('foo', 'bar', []);

    Reminder::error('foo', 'bar', []);

    reminder()->info('foo', 'bar', []);

---

### demo

    <?php

    Route::get('/', function () {
        Reminder::success('Hi! this is Reminder', 'Hello', ["positionClass" => "toast-bottom-right"]);

        return view('welcome');
    });

    <!DOCTYPE html>
    <html>
        <head>
            <title>Laravel</title>
            <link rel="stylesheet" href="http://cdn.bootcss.com/toastr.js/latest/css/toastr.min.css">
        </head>
        <body>
            <div class="container">
                <div class="content">
                    <div class="title">Laravel 5</div>
                </div>
            </div>

            <script src="http://cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
            <script src="http://cdn.bootcss.com/toastr.js/latest/js/toastr.min.js"></script>
            {!! reminder()->message() !!}
        </body>
    </html>

<img src="http://ww3.sinaimg.cn/mw690/baa3278fgw1ey7ky56nbgj20n60fuaav.jpg" alt="" class="shadow">

### Options

You can set custom options for Reminder. Run:

    php artisan vendor:publish

to publish the config file for reminder.

You can see [toastr's documentation](http://codeseven.github.io/toastr/demo.html) to custom your need.

### MIT
