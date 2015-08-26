---
layout: post
title: "laravel快速创建组件--如优酷api"
date:   2015-08-26 21:20
categories: Laravel
excerpt: laravel快速创建组件--如优酷api
---

使用的 Laravel 中的服务容器来创建自己的服务，下面是一个优酷视频搜索的 demo，其中需要使用到 [guzzle](docs.guzzlephp.org) 来请求服务器.

首先新建我们的 YoukuServiceProvider.php

    // app/Media/Providers/YoukuServiceProvider.php
    namespace App\Media\Providers;

    use App\Media\Youku\YoukuApi;
    use Guzzle\Service\Client;
    use Illuminate\Support\ServiceProvider;

    class YoukuServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap the application services.
         *
         * @return void
         */
        public function boot()
        {
            //
        }

        /**
         * Register the application services.
         *
         * @return void
         */
        public function register()
        {
            $this->app->bind('youku', function() {

                $client = new Client('https://openapi.youku.com/');

                return new YoukuApi($client);

            });

        }
    }

这里绑定了我们的 youku 服务，将 app/Media/Providers/YoukuServiceProvider 添加到 app.php 中注册，接下来创建 YoukuApi.php

    // app/Media/Youku/YoukuApi.php
    namespace App\Media\Youku;

    use Guzzle\Service\Client;

    class YoukuApi
    {
        protected $client;

        public function __construct(Client $client)
        {
            $this->client = $client;
        }

        public function search($keyword, $page = 1, $count = 20)
        {
            $uri = '/v2/searches/video/by_keyword.json?client_id='
                . config('media.youku.client_id')
                . '&keyword=' . urldecode($keyword)
                . '&page=' . urldecode($page)
                . '&count=' . $count;

            return $this->client->get($uri)->send()->json();
        }
    }

youku 配置信息可以写在 config/media.php 中

    // config/media.php
    return [

        'youku' => [
            'client_id' => env('YOUKU_ID', false),
            'client_secret' => env('YOUKU_SECRET', false)
        ]

    ];

最后是我们的 Facade

    // app/Media/Facades/Youku.php
    namespace App\Media\Facades;

    use Illuminate\Support\Facades\Facade;

    class Youku extends Facade
    {
        protected static function getFacadeAccessor()
        {
            return 'youku';
        }
    }

然后在 app.php 的 aliases 中添加

    'Youku'     => App\Media\Facades\Youku::class,

下面来测试我们的代码

    ####################################################
    # Youku search
    ####################################################
    get('youku/{keyword}/{page?}/{count?}', function($keyword, $page = false, $count = false) {

        dd(Youku::search($keyword, $page, $count));

    });

这样一个自己的组件就完成了，完结！