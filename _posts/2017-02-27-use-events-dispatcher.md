---
layout: post
title: "从事件系统说起，更好的组织代码"
subtitle:   ""
date:   2017-02-27
author: "RryLee"
tags:
    - php
    - laravel
---

> Object-oriented code has gone a long way to ensuring code extensibility. By creating classes that have well defined responsibilities, your code becomes more flexible and a developer can extend them with subclasses to modify their behaviors. But if they want to share the changes with other developers who have also made their own subclasses, code inheritance is no longer the answer.

摘自 [Symfony EventDispatcher Component](http://symfony.com/doc/current/components/event_dispatcher.html)，确实，对于一些的难以维护的代码(几乎快要成为一个黑盒，可能经历了3-4待程序员之手)，面对新的 Feature，你需要在保持旧的逻辑不受到任何影响，最好的方式就是去在代码从添加事件(如果可以的话，务必这么做，特别是对于 TP 的项目，总有一天所有的 TP 代码都会迁移到 Laravel 上面)。

最近，产品说，我们的用户需要在完成一项操作之后收到微信推送的消息，同时要受到邮件，和短信。对于这个需求，想必在没有事件驱动的情况下，可想而知代码有多丑陋。在明确了你需要使用这种编程思想之后，大概在产品和你说完这个需求之后，核心代码应该已经生成了。

```php

OperationCompleted::class => [
    PushWechatNotification::class,
    SendEmailNotification::class,
    SendSmsNotification::class,
],

```

接下来的事情也就水到渠成了。编程中是以不变应万变的抽象永远是最好的设计模式。

后面的是说代码的事情了，对事件非常了解的同学可以右上角了。分割线!

----

之前一直以为 laravel 的 [events](https://github.com/illuminate/events) 是对 Symfony 的事件系统的一个封装，后来看了源码之后发现不是，看完 Symfony 事件源码之后确实收获很大。

[ImmutableEventDispatcher](https://github.com/symfony/event-dispatcher/blob/master/ImmutableEventDispatcher.php) 这里对 EventDispatcher 做了一层代理（当时就想到了 [immutable-js](https://github.com/facebook/immutable-js/)），对于公司底层核心业务就很有用了，毕竟永远不知道公司的新同事或者实习生会写出什么样的代码，对于这种不变的代码还是非常有有必要的，同时也符合现在函数式的哲学思想。

----

有个小技巧，对于同一个事件触发的很多不同的监听器，类似上面发送提醒的功能，使用订阅者去解决获取更为优雅。

```php
<?php

use Symfony\Component\EventDispatcher\Event;
use Symfony\Component\EventDispatcher\EventDispatcher;
use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Symfony\Component\EventDispatcher\ImmutableEventDispatcher;

require __DIR__ . '/vendor/autoload.php';

$dispatcher = new EventDispatcher;

class OperationSubscriber implements EventSubscriberInterface
{
	public static function getSubscribedEvents()
	{
		return [
			'operation.completed' => [
				['pushWechatNotification', 1],
				['sendEmailNotification', 2],
				['SendSmsNotification', 3],
			],
		];
	}

	public function pushWechatNotification(Event $event)
	{
		var_dump('pushWechatNotification');
	}

	public function sendEmailNotification(Event $event)
	{
		var_dump('sendEmailNotification');
	}

	public function SendSmsNotification(Event $event)
	{
		var_dump('SendSmsNotification');
	}
}

$dispatcher->addSubscriber(new OperationSubscriber);
$immutableDispatcher = new ImmutableEventDispatcher($dispatcher);
$immutableDispatcher->dispatch('operation.completed');
```

最后祝愿用 TP 的同学在项目中能更多的使用 laravel 组件，写更多 laravel 风的代码！
