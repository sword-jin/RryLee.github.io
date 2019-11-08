---
layout: post
title: "优化 yii curl 减少 tcp 开销"
subtitle:   ""
date:   2019-04-19
author: "RryLee"
tags:
    - php
---

* content
{:toc}


我们需要可以改进什么：

思考一下，现有业务中，一个接口对下游的同一个服务的多次调用（包括调用多个接口，分页获取），都可以进行下面的优化。

# 背景

前端时间公司购物车服务从代理层到服务docker改为了长连接，效果如下图：

<img src="/img/posts/image2019-11-6 15_29_49.png" class="img-responsive">

established 数量符合预期，但是 timewait 虽然降低了 50% 左右，但是整体上来说，依然不符合预期，从线上机器查看当前 tcp 连接状态：

```bash
[root@container-10-3-53-182 ~]# netstat -antp|awk '{print $5}'|sort|uniq -c
      4 0.0.0.0:*
      1 10.0.4.12:80
      1 10.3.52.16:6402
      1 10.3.53.15:1998
      1 10.4.10.10:80
    129 10.4.10.10:9438
     39 10.4.10.10:9442
      1 10.4.242.18:29048
      1 10.4.242.18:44936
      1 10.4.242.18:9716
      1 10.4.242.22:28894
      1 10.4.242.22:30678
      1 10.4.242.22:41940
      1 10.4.242.22:56414
      1 10.4.242.2:37462
      1 10.4.242.2:44638
      1 10.4.242.2:7020
      1 10.4.242.6:44572
      1 10.4.242.6:49338
      1 10.4.242.6:57334
      1 10.9.61.22:55630
      1 10.9.81.104:9426
```

主要是对下游两个http服务的连接。

在 php 中 curl_close 会关闭 tcp 连接，所以产生 time_wait 这个问题无解。

# 抓包

查看浏览服务更新商品缓存的接口，发现对下游同一个ip+port(base服务)，多次调用存在多次 tcp 建立释放，这显然是不合理的。

虽然现有的 fpm 在处理完一次 cgi 之后，会自动释放这些资源，但是，在一次请求中，我们是可以做到复用 tcp 连接的。

<img src="/img/posts/image2019-11-6_15_34_46.png" class="img-responsive">

查看代码发现，我们所有的 rpc 请求都是使用的 yii/curl 这个库

每次请求都会去 curl_init 初始化，所以产生以上的问题理所应当。

<img src="/img/posts/image2019-11-6_15_38_20.png" class="img-responsive">

# 优化

我知道 [guzzle](https://github.com/guzzle/guzzle) 这个 php http client 是业内最为止认可的 package 了，现在只是去验证一下，guzzle 是做了复用资源具柄优化的。

<img src="/img/posts/image2019-11-6_15_44_35.png" class="img-responsive">

简单的修改之后测试：

<img src="/img/posts/image2019-11-6_15_47_31.png" class="img-responsive">

不仅如此，还添加了另外一个域的抓取，tcp连接在单次fpm处理中得到了复用

```
<?php

namespace app\components;

class HttpClient
{
    public static $client;

    public static function getClient()
    {
        if (static::$client == null) {
            static::$client = new \GuzzleHttp\Client;
        }
        return static::$client;
    }
}

// ...
$client = HttpClient::getClient();
$response = $client->post($url, [
    'body' => json_encode($params),
    'headers' => $header,
]);
// ...
```

# 问题

这个优化只是优化了当前代码中的一个小问题，并没有从根本上解决 php 服务里面的 time_wait 过多的问题。

我们的限流工作一定要在上游去处理，才能真正保证下游服务的稳定。

反过来思考，下游服务也是可以通过计算 tw 的量，来判断上游的调用是否合理。

