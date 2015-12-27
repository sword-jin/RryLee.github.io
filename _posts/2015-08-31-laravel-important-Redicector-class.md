---
layout: post
title: "laravel 中重要的 Redirector 类--默认中间件解读"
date:   2015-08-30
subtitle: ""
author: "RryLee"
tags:
    - laravel
---

laravel的 Redirector 类帮助我们快速构建页面逻辑，我们最常用的 `Auth::guest()`, `Auth::check()` ... 都是以它为基础。

    use Illuminate\Http\RedirectResponse;
    use Illuminate\Session\Store as SessionStore;

从这里可以看到它对这两个类进行了二次封装，现在已经能猜想这个类大概是处理什么的了(SessionStore)。。。

一些基础的函数就不用说明了，说到几个中间件，可以找到里面的 `guest` 方法

    public function guest($path, $status = 302, $headers = [], $secure = null)
    {
        $this->session->put('url.intended', $this->generator->full());

        return $this->to($path, $status, $headers, $secure);
    }

这里可以看到 `当前访问的url` 会被存入key 为 url.intended的session中。这里为什么会这样说，你沿着 `$this->generator->full()` 这个函数向上找，就可以看到答案。不再赘述。

---

看一个简单的例子

    get('test', ['middleware' => 'auth',
        function() {
            return 'auth';
        }
    ]);

    get('auth/login', function() {
        dd(Session::get('url.intended'));
    });

访问 localhost:8000/test 跳转到 localhost:8000/auth/login，并且给我们返回 localhost:8000/test，这个值会被用在成功登录的跳转地址。

