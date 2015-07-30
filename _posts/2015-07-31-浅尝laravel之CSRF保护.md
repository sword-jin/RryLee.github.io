---
layout: post
title: "php 用户认证模块笔记"
feature-img: "img/googel.jpg"
---

之前看到一篇关于跨站攻击的文章，于是乎就想了解下 laravel 中关于 CSRF( Cross-Site-Request-Forgery) 保护是怎样完成的。

就一般来说，现在的主流框架都会使用中间件(Middleware)，判断 HTTP verb，然后在 POST, PUT, PATCH, DELETE 这些请求时，将一段随机字符串哈希值存入 session，然后和 POST, PUT, PATCH, DELETE 过来的 token 相比较，即可完成 CSRF 保护。下面是我在 slim 中的一般处理情况：

```php
class CsrfMiddleware extends Middleware
{
    ...
    public function check()
    {
        // 生成 session
        if (! isset($_SESSION[$this->key])) {
            $_SESSION[$this->key] = $this->app->hash->hash($this->app->randomlib->generateString(128));
        }

        $token = $_SESSION[$this->key];

        // 对比 session 和 提交过来的 token
        if (in_array($this->app->request()->getMethod(), ['POST', 'PUT', 'DELETE', 'PATCH'])) {
            $submittedToken = $this->app->request()->post($this->key) ?: '' ;
            if (! $this->app->hash->hashCheck($token, $submittedToken)) {
                throw new Exception('CSRF Token mismatch');
            }
        }
        // 将 csrf 的键值对共享到所有视图，以供视图提交
        $this->app->view()->appendData([
            'csrf_key' => $this->key,
            'csrf_token' => $token
        ]);
    }
    ...
}
```
下面来看看 laravel 中怎么处理的，首先在 /app/Http/Kernel.php 可以看到我们的 $middleware 数组中包含5个中间件，\App\Http\Middleware\VerifyCsrfToken 继承自 Illuminate\Foundation\Http\Middleware\VerifyCsrfToken ，现在找到几个关键的函数

```php
    protected function tokensMatch($request)
    {
        // 获取请求体中的 name=_token 的值，不存在获取请求头部中的 X-CSRF-TOKEN 的值
        $token = $request->input('_token') ?: $request->header('X-CSRF-TOKEN');
        // 如果以上两个都没有，则获取头部中的 X-XSRF-TOKEN 的值  ????
        if (!$token && $header = $request->header('X-XSRF-TOKEN')) {
            $token = $this->encrypter->decrypt($header);
        }
        // session 中 token 的值和 token的值
        return Str::equals($request->session()->token(), $token);
    }
```

在 laravel4 中，我们表单中会这样写，<input type="hidden" name="_token" value="{{ Session::token() }}">，在 laravel5.1 中我们这样简写 {!! csrf_field() !!}。关于 X-CSRF-Token，我们这样使用 ajax

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

最后是 X-XSRF-TOKEN，就我了解的，在一些前端 JS 框架中，会在头部中自动加入 X-CSRF-TOKEN，例如 angular 是这样的：

```js
var xsrfValue = urlIsSameOrigin(config.url)
    ? $browser.cookies()['XSRF-TOKEN']
    : undefined;
if (xsrfValue) {
    reqHeaders['X-XSRF-TOKEN'] = xsrfValue;
}
```

接下来看这个函数：

    ...
    public function handle($request, Closure $next)
    {
        if ($this->isReading($request) || $this->shouldPassThrough($request) || $this->tokensMatch($request)) {
            return $this->addCookieToResponse($request, $next($request));
        }

        throw new TokenMismatchException;
    }
    ...
    protected function isReading($request)
    {
        return in_array($request->method(), ['HEAD', 'GET', 'OPTIONS']);
    }
    ...

laravel 中使用 isReading 来判断请求的类型，来 http 请求中，默认 HEAD,GET,OPTIONS 是安全的，这里可以参考 w3c 的这篇[文章](http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)。

在之前的数组 $middleware 中还有 Illuminate\Session\Middleware\Illuminate\Session\Middleware\StartSession，这个中间件负责开启 Session，这是构造函数:

    public function __construct(SessionManager $manager)
    {
        $this->manager = $manager;
    }

这里传入一个 SessionManager 对象，也就是用来管理 Session 的类实例，接下来看我们的 Illuminate\Support\Facades\Session.php

```php
/**
 * @see \Illuminate\Session\SessionManager
 * @see \Illuminate\Session\Store
 */
class Session extends Facade
{
    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor()
    {
        return 'session';
    }
}
```

看到上面的提示找到我们 \Illuminate\Session\Store.php 后面真相了。

    ...
    public function start()
    {
        $this->loadSession();

        if (!$this->has('_token')) {
            $this->regenerateToken();
        }

        return $this->started = true;
    }
    ...
    public function regenerateToken()
    {
        $this->put('_token', Str::random(40));
    }
    ...

通过这两个方法来产生 token，里面还有各种 session 存，取，添加函数。这里就不一一列举了。最后要提醒的是，laravel 试图中的 token 是从 session 中取值，与之前 slim 中 token 共享给视图还是有些区别的。

最后，看了下 session 的各种方法，其底层大都来自 Symfony，有兴趣有时间的同学也可以去阅读下源码。
