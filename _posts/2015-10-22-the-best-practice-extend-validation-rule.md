---
layout: post
title: "laravel 拓展验证规则最佳实践"
date:   2015-10-22 11:00
categories: laravel
excerpt: laravel 拓展验证规则最佳实践
---

* content
{:toc}

# 引出

有时候，[laravel validation](http://laravel.com/docs/5.1/validation) 里面自带的规则不能满足实际需求的时候。我们就需要来拓展自己的规则，那么，what's the best practice?

# 接口

首先在 `laravel api` 中可以找到这个验证类的[拓展接口](http://laravel.com/api/5.1/Illuminate/Contracts/Validation/Factory.html#method_extend)

    void extend(string $rule, Closure|string $extension, string $message = null)

    Register a custom validator extension.

可以看到我们能定义规则和错误信息了。

文档中也有提到拓展的使用:

    public function boot()
    {
        Validator::extend('foo', function($attribute, $value, $parameters, $validator) {
            return $value == 'foo';
        });
    }

这里的四个参数 $attribute, $value, $parameters, $validator 分别代表着,
举个例子, "score" => "mustIn:0,100"，即

    $attribute 代表 score
    $value 代表 $_POST['score']
    $parameters 为一个参数数组 [0=>'0', 1=> 100]
    $validator 代表$validator这个对象本身

# 实现

一般我会在 app 目录下新建 Extends\Validators.php

接着将其添加到 AppServiceProvider.php 下，或者其他服务类：

    use use Illuminate\Validation\Factory;

    public function boot(Factory $validator)
    {
        require_once app_path() . '/Extends/Validators.php';
    }

# demo

假设有个表单post这些数据过来。

    // routes.php

    post('form', function(Illuminate\Http\Request $request) {
        $validator = Validator::make($request->all(), [
            'username' => 'required|min:3',
            'phone' => 'required|phone',
            'score' => 'required|score:12, 12'
        ]);

        if ($validator->fails()) {
            return redirect()->back()
                ->withErrors($validator)
                ->withInput();
        }
    });

    // validators.php

    $validator->extend(
        'phone',
        function($attribute, $value, $parameters) {
            return preg_match("/^1[3|4|5|7|8]\d{9}$/", $value);
        },
        "the number is not a valid phone."
    );

    $validator->extend(
        'score',
        function($attribute, $value, $parameters) {
            return ($value >= 0) and ($value <= 100);
        },
        "the score is not a valid score."
    );

这样就完成了拓展类，结构也比较清晰。

# 总结

当然，也还有其他可以使用的模式，不过都大同小异。

# 补充

当我们的拓展规则只是 string invalid 这种类型的时候，没有与数据之间的交互，或者需要使用到其他规则时，可以使用 [regular expression](http://laravel.com/docs/5.1/validation#rule-regex) 来进行数据过滤：

比如之前例子中的 phone 就可以这样来写

    'phone' => ['required', 'regex:/^1[3|4|5|7|8]\d{9}$/'],

这里有个需要注意的是多规则必须使用数组来填写。
