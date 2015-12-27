---
layout: post
title: "[译]处理Laravel Api应用中的异常"
date:   2015-09-28
subtitle: ""
author: "RryLee"
tags:
    - laravel
---

* content
{:toc}

[原文地址](http://culttt.com/2015/08/10/dealing-with-exceptions-in-a-laravel-api-application/) -- _POSTED BY [PHILIP BROWN](http://culttt.com/author/philipbrown/) ON AUGUST 10TH, 2015_

异常在控制应用的处理流程中是很重要的方法。当一个应用请求出现问题，立即停止执行然后采取另一个行动通常是重要的。

处理 API 中的问题尤其重要，API 响应是给用户的接口所以你需要对错误确保有一份详尽的描述解释，它包含 HTTP 状态码，和文档相关的错误码，以及可读的错误描述。

在今天的介绍我会展示我是怎么使用异常来构建我的Laravel API 应用的。这种构造让应用很容易从API返回详细的描述性错误响应，当然让测试也变得更容易。

在一个简短的解析之后我返回写Laravel 应用，一段时间的关于 PHP week 写作，我想我真的很烦，需要休息一下了。

然而，在过去几个月我一直在做我最好的作品Laravel API应用。这给了一些新的想法和技巧想与你们分享。

不用开始一个新的项目，只是使用之前的 Cribbb series，所有的代码都来自之前的教程并且能在 github 上找到。

# 理解 HTTP 状态码

(这段讲了一点 HTTP 状态码的基础，就略了)

[Wikipedia page](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

# 关于异常工作

无论什么时候从API返回一个响应都必须使用一个标准的HTTP状态码。我们必须只用正确的响应吗来描述情况。

使用不正确的响应码真的很糟糕，因为你给用户一个错误的API信息。例如，如果你返回一个200代替400，用户就不会认为他们的请求不合法。因此，在应用中我们应该分类任何可能出错的情况来作为一个标准的HTTP响应码。

例如，如果客户端请求不存在资源就应该返回 `404 Not Found` 响应。从你的代码触发响应，我们能抛出匹配的 `NotFoundException` 异常，要做到这样我们能为每个HTTP状态码创建基础的异常类。

接下来，在程序代码中我们能创建特殊的异常，拓展基础 HTTP 异常类来提供具体错误的细节。例如，我们有一个 `UserNotFound` 异常拓展 `NotFoundException` 基础异常类，这意味着在一个特殊环境下我们能抛出一个特别的异常，对于能够浮出表面的问题。应用将自动的从基础异常类返回正确的 HTTP 响应。

最后我们也要提供一份描述性的解释关于哪里出错了。我们能获取这份信息通过定义异常类抛出的错误信息。

讲了这么多，来看代码吧。

# 创建错误配置文件

对于错误提供API响应描述性的解释是很重要的，如果不提供错误细节，用户就需要努力的解决这个问题了。

要保证所有可能的错误响应在一个地方，我准备创建一个 `errors.php` 配置文件在 `config` 目录下，这意味这在一个地方创建错误文档变得容易。

它还应该很容易提供语言翻译错误，而不是试图深入代码以找到每一个错误!

首先我创建了一些标准的 HTTP 错误响应：

    // config/errros.php

    return [

        /*
        |—————————————————————————————————————
        | Default Errors
        |—————————————————————————————————————
        */

        'bad_request' => [
            'title'  => 'The server cannot or will not process the request due to something that is perceived to be a client error.',
            'detail' => 'Your request had an error. Please try again.'
        ],

        'forbidden' => [
            'title'  => 'The request was a valid request, but the server is refusing to respond to it.',
            'detail' => 'Your request was valid, but you are not authorised to perform that action.'
        ],

        'not_found' => [
            'title'  => 'The requested resource could not be found but may be available again in the future. Subsequent requests by the client are permissible.',
            'detail' => 'The resource you were looking for was not found.'
        ],

        'precondition_failed' => [
            'title'  => 'The server does not meet one of the preconditions that the requester put on the request.',
            'detail' => 'Your request did not satisfy the required preconditions.'
        ]

    ];

开发这个应用我可以添加这个列表，提供一个链接到相关的文档页面也是一个好主意。在将来的某个时候我能简单的添加到每个错误。

# 创建抽象异常

接下来我要创建一个抽象异常类，应用所有的特殊异常都将于此拓展。

    namespace Cribbb\Exceptions;

    use Exception;

    abstract class CribbbException extends Exception
    {
        /**
         * @var string
         */
        protected $id;

        /**
         * @var string
         */
        protected $status;

        /**
         * @var string
         */
        protected $title;

        /**
         * @var string
         */
        protected $detail;

        /**
         * @param @string $message
         * @return void
         */
        public function __construct($message)
        {
            parent::__construct($message);
        }
    }

这使捕获应用的所有特殊异常都变得容易，并且提供了一个不错的分离与其他可能会在应用执行中抛出的潜在异常。

对于每个异常，我提供 `id`, `status`, `titile` 和 `detail`。这是向 [JSON API](http://jsonapi.org/) 规范靠拢。

我也提供一个 `getStatus` 方法：

    /**
     * Get the status
     *
     * @return int
     */
    public function getStatus()
    {
        return (int) $this->status;
    }

`JSON API` 规范规定状态码必须为一个字符串。我将其转为int以便我能向 Laravel 提供正确的响应码。

我也提供一个 `toArray` 方法来返回异常数组，只是为了方便：

    /**
     * Return the Exception as an array
     *
     * @return array
     */
    public function toArray()
    {
        return [
            'id'     => $this->id,
            'status' => $this->status,
            'title'  => $this->title,
            'detail' => $this->detail
        ];
    }

最后我能从 `errros.php` 文件获取，每个特殊错误的 `title` 和 `detail`，要做到这样，我必须接受异常 id 当一个新的异常被初始化。我将使用这个 id 来得到 `title` 和 `detail`。

所以，抛出一个异常应该像这样：

    throw new UserNotFound('user_not_found');

当然，在某些情况下我也希望提供具体细节的异常。例如我想提供用户的 id 在异常的细节里，为此我需要异常能接受任意数量的参数：

    throw new UserNotFound('user_not_found', $id);

设置异常我使用下面的方法：

    /**
     * Build the Exception
     *
     * @param array $args
     * @return string
     */
    protected function build(array $args)
    {
        $this->id = array_shift($args);

        $error = config(sprintf('errors.%s', $this->id));

        $this->title  = $error['title'];
        $this->detail = vsprintf($error['detail'], $args);

        return $this->detail;
    }

在这个方法我先弹出第一个参数作为 id，然后使用 id 来从 `errors.php` 中获取 `title` 和 `detail`，接下来找到余下的参数传入 `detail`。最后我能返回 `detail` 作为默认的异常的消息。

# 创建基础异常类

有了抽象异常我能创建基础异常了：

    namespace Cribbb\Exceptions;

    class NotFoundException extends CribbbException
    {
        /**
         * @var string
         */
        protected $status = '404';

        /**
         * @return void
         */
        public function __construct()
        {
            $message = $this->build(func_get_args());

            parent::__construct($message);
        }
    }

每个基础异常类都提供状态码，调用构造方法来调用 build() 方法同时传递 message 到父类。你现在可以创建这些简单的异常类来表示每个应用程序将使用的 HTTP 状态代码。如果你需要添加一个新的 HTTP 状态码响应，很简单的创建一个子类就可以了。

# 创建应用异常

最后我们能在我们的程序代码中使用这些 HTTP 异常来提供特殊的异常。例如我们有一个 `UserNotFound` 异常：

    namespace Cribbb\Users\Exceptions;

    use Cribbb\Exceptions\NotFoundException;

    class UserNotFound extends NotFoundException
    {

    }

现在无论什么时候你想要找到一个 user, 但是用户不存在，你都可以抛出这个异常。这个异常冒泡到表面同时正确的 HTTP 响应带有错误信息将被自动返回。这表示如果异常被抛出，你可以不做什么，你不是必须捕获它因为用户需要被提醒当 user 没有被找到。

在测试中你能 assert 一个 UserNotFound 被抛出，代替一个通用的 NotFound 异常，这表示你编写测试时可以有信心的测试失败原因。这让你的测试变得更加容易理解。

# 处理异常正确响应

Laravel 允许你处理异常，返回响应在 Handler.php 文件中，第一件要做的事情就是添加 CribbbException 类到 $dontReport  数组中。

    /**
     * A list of the exception types that should not be reported
     *
     * @var array
     */
    protected $dontReport = [
        HttpException::class,
        CribbbException::class
    ];

当特殊异常被抛出时我不需要被告知，因为这是预料的。通过拓展 CribbbException 类是的捕获应用的所有特殊异常都变得非常容易。接着去更新 render() 方法只呈现异常，如果调试配置设为 true，否则我们就能处理异常在 handle() 方法中：

    /**
     * Render an exception into an HTTP response
     *
     * @param Request $request
     * @param Exception $e
     * @return Response
     */
    public function render($request, Exception $e)
    {
        if (config('app.debug')) {
            return parent::render($request, $e);
        }

        return $this->handle($request, $e);
    }

最后我们能转化异常为 `JsonResponse` 在handle() 方法中：

    /**
     * Convert the Exception into a JSON HTTP Response
     *
     * @param Request $request
     * @param Exception $e
     * @return JSONResponse
     */
    private function handle($request, Exception $e) {
        if ($e instanceOf CribbbException) {
            $data   = $e->toArray();
            $status = $e->getStatus();
        }

        if ($e instanceOf NotFoundHttpException) {
            $data = array_merge([
                'id'     => 'not_found',
                'status' => '404'
            ], config('errors.not_found'));

            $status = 404;
        }

        if ($e instanceOf MethodNotAllowedHttpException) {
            $data = array_merge([
                'id’     => 'method_not_allowed',
                'status’ => '405'
            ], config('errors.method_not_allowed'));

            $status = 405;
        }

        return response()->json($data, $status);
    }

对于 CribbbException 类能简单的调用 toArray() 方法来返回异常数组同时 getStatus() 方法来返回 HTTP 状态码。我们能处理其他异常在这个方法中，如你所见，我捕获了 `NotFoundHttpException` 和 `MethodNotAllowedHttpException` 在这个例子中，所以我能返回正确的响应。

最后我能返回 `JsonResponse` 通过使用 response() 帮助方法的 json() 方法来返回 $date 和 $status。

# 总结

异常是应用开发的一个非常重要的方面，它们是控制应用执行流程的优秀工具。在特殊情况下需要停止应用程序并返回一个错误，而不是继续执行了，异常使这很容易实现。API总是返回正确的HTTP状态代码是很重要的，应用的 API 接口，所以让你遵循认可的标准和协议是非常重要的。你还需要返回类可读的错误消息，以及提供最新的问题文档和如何解决它。在今天的教程中,我们已经创建了一个应用程序中来使用异常的基础通过为每个HTTP状态代码来创建基类。

每当一个问题在应用程序中我们没有理由不为这个问题返回特殊的 HTTP 状态代码。我们也建立一个简单的方法为每一个可能出错的事情列出详细的错误消息。这将是容易保持最新的,因为它们都在一个地方。

最后,我们已经创建了一个简单的方法来在应用程序中使用异常。通过扩展这些基础异常我们可以创建一个细节的异常在我们的应用程序代码。这让编写测试是你能简单的 assert 准确的异常，在响应的特殊环境下。

同时也使它很容易处理异常,因为十有八九的你只需要异常冒泡到表面。除了到达表面时,正确的HTTP状态代码和错误消息将自动返回给客户端。

> 注：有些地方稍作了修改
