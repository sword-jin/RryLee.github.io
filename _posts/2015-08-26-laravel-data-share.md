---
layout: post
title: "laravel中的视图数据共享"
date:   2015-08-26 15:020
categories: Laravel
excerpt: laravel中的视图数据共享
---

* content
{:toc}

总结了几种laravel中数据共享的方法

## 使用AppServiceProvider

直接在 AppServiceProvider 中添加：

    view()->composer('*', function($view) {
        $foo = 'bar';
        $view->with(compact('foo'));
    });

*共享所有模版，在blade模版中直接 {{ $foo }} 来引用

## 创建基础控制器

    class BaseController extends Controller
    {
        $foo;

        public funciton __construct()
        {
            $this->foo = 'bar';
        }
    }

控制器通过继承它来得到共享数据

    class FooController extends BaseController
    {
        public function foo()
        {
            dd($this->foo);
        }
    }

## 创建 ComposerServiceProvider

    // ComposerServiceProvider.php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        public function boot()
        {
            view()->composer(
                'foo.index', 'App\Http\ViewComposers\FooComposer'
            );
            // ...
        }

        public function register()
        {
            # code...
        }
    }

    // FooComposer.php
    namespace App\Http\ViewComposers;

    use Illuminate\Contracts\View\View;

    class FavoritesComposer
    {
        public function compose(View $view)
        {
            $view->with('foo', $bar);
        }
    }

在app.php中注册服务之后就可以在 post/index.blade.php 就可以获取数据了。以上