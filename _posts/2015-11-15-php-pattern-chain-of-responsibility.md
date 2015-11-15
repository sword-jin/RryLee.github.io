---
layout: post
title: "php设计模式-责任链模式"
date:   2015-11-15 14:00
categories: 设计模式
excerpt: php设计模式-责任链模式
---

* content
{:toc}

# 概述

责任链模式是一种对象的行为模式。在责任链模式里，很多对象由每一个对象对其下家的引用而连接起来形成一条链。请求在这个链上传递，直到链上的某一个对象决定处理此请求。发出这个请求的客户端并不知道链上的哪一个对象最终处理这个请求，这使得系统可以在不影响客户端的情况下动态地重新组织和分配责任。

---

# 场景

* 在处理请求时可以使用，自动选择处理方式，过滤某些信息。(laravel 中的中间件有使用到)

* 日志记录管理，也是可以选择各种记录方式。

* 举报系统，根据具体情况来处理用户。

---

# Demo

先描述下问题，假设现在需要处理一个**删除文章**的请求，我们需要判断**是否登录** -> **是否为文章的所有者** -> **是否为管理员**，我们会顺着这个流程来判断，来做出响应的处理。

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1ey1m8fg6h0j20ha0cuq3o.jpg)

使用 UserStatus 来模拟用户状态

    abstract class Checker
    {
        protected $successor;

        abstract public function check(UserStatus $user);

        public function succeedWith(Checker $successor)
        {
            $this->successor = $successor;
        }

        public function next(UserStatus $user)
        {
            $this->successor->check($user);
        }
    }

    class AuthChecker extends Checker
    {
        public function check(UserStatus $user)
        {
            if (! $user->isLogin) {
                throw new Exception('This user not login.');
            }

            $this->next($user);
        }
    }

    class OwnerChecker extends Checker
    {
        public function check(UserStatus $user)
        {
            if (! $user->isOwner) {
                throw new Exception('This user is not the owner of this article.');
            }

            $this->next($user);
        }
    }

    class AdminChecker extends Checker
    {
        public function check(UserStatus $user)
        {
            if (! $user->isAdmin) {
                throw new Exception('This user is not admin.');
            }

            $this->next($user);
        }
    }

    class UserStatus
    {
        public $isLogin = true;
        public $isOwner = true;
        public $isAdmin = false;
    }

    $authChecker = new AuthChecker;
    $ownerChecker = new OwnerChecker;
    $adminChecker = new AdminChecker;

    $authChecker->succeedWith($ownerChecker);
    $ownerChecker->succeedWith($adminChecker);

    try {
        $authChecker->check(new UserStatus);
    } catch (Exception $e) {
        print $e->getMessage();
    }

可以看到我们的责任链已经形成 `AuthChecker` -> `OwnerChecker` -> `AdminChecker`，处理请求时客户端是不知道在哪里处理的。

# 总结

个人感觉责任链模式可能会在不经意使用到，可能会没有注意到，这样的代码有种很库的感觉，缺点的话确实有点略显繁琐。
