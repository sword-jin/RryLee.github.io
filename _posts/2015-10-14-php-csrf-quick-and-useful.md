---
layout: post
title: "php快速有效的防止csrf攻击"
date:   2015-10-15 11:58
categories: PHP
excerpt: php快速有效的防止csrf攻击
---

* content
{:toc}

# 闲扯

在之前的一篇文章中谈到了 laravel 是怎么来防止csrf攻击的，但是如果你使用原生php开发或者一些微型框架就需要自己来完成了。

# 认识

*跨站请求伪造*（英语：Cross-site request forgery），也被称为 one-click attack 或者 session riding，通常缩写为 CSRF 或者 XSRF， 是一种挟制用户在当前已登录的Web应用程序上执行非本意的操作的攻击方法。[1] 跟跨网站脚本（XSS）相比，XSS 利用的是用户对指定网站的信任，CSRF 利用的是网站对用户网页浏览器的信任。

这利用了web中用户身份验证的一个漏洞：简单的身份验证只能保证请求发自某个用户的浏览器，却不能保证请求本身是用户自愿发出的。

# 解决方法

在我们的表单中加入一个随机 token，请求到服务器时会验证这个表单是否与session中的token相匹配，这样服务器就能有效的拒绝非法请求了。

# demo

    //test.php
    <?php
    session_start();

    $token = base64_encode(openssl_random_pseudo_bytes(32));
    $_SESSION['token'] = $token;
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>csrf demo</title>
    </head>
    <body>
        <form action="token.php" method="POST">
            <input type="hidden" name="token" value="<?= $token ?>">
            <input type="submit" value="Submit">
        </form>
    </body>
    </html>

    // test.php
    <?php
    session_start();

    if ($_SERVER['REQUEST_METHOD'] == 'POST' and (! isset($_POST['token']) || $_POST['token'] == $_SESSION['token'])) {
        echo 'request success';
    } else {
        echo 'csrf forbidden';
    }

这样简单的验证就能防止csrf攻击了

# 总结

如果你需要自己的 Token 类，其实你可以参考这样来做；

https://gist.github.com/RryLee/4412e17b5c3285fc7f6f

