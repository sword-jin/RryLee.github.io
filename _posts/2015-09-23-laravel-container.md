---
layout: post
title: "浅尝laravel之解读 Service Container"
date:   2015-09-23
subtitle: ""
author: "RryLee"
tags:
    - laravel
---

* content
{:toc}

# 概述

一直觉得 laravel 中的 IoC 很神奇，让我们的代码结构更加清晰，测试更加容易，coding也更有feeling，我感觉是时候看一波 lararavel/container 的源码。

首先，看几条熟悉的代码

    class UserController extends Controller
    {
        protected $foo;

        public function __construct(FooInterface $foo)
        {
            $this->foo = $foo;
        }
    }

这里 FooInterface 是一个接口，相信刚使用 laravel 依赖注入的同学都会有这样的疑问，laravel 是怎么实例化正确的对象的，一般我们会在 ServiceProvider 中绑定接口和类。比如

    $this->app->bind('App\FooInterface', 'App\Foo');

这样 laravel 就会帮你实例化依赖注入的类了，但是，为什么呢？

---

# 准备工作

为了更好的理解 Laravel Service Provider，首先，准备好一些测试的文件(其实加载 illuminate/container 就ok了):

<img class="shadow" src="http://ww3.sinaimg.cn/mw690/baa3278fgw1ewc6eh7q47j205d08574b.jpg" />

来看看常用的3中绑定方法:

    // index.php
    require 'vendor/autoload.php';

    use Illuminate\Container\Container;

    $container = new Container;

    $container->bind('foo', 'Acme\Foo');

    $foo = $container->make('foo');
    var_dump($foo === $container->make('foo')); // false

    $container->singleton('greet', 'Acme\Greet');
    $greet = $container->make('greet');

    var_dump($greet === $container->make('greet')); // true

    $mail = new Acme\Mail;
    $container->instance('mail', $mail);

    $newMail = $container->make('mail');
    var_dump($newMail === $mail && $newMail === $container->make('mail')); // true

通过上面的例子我们可以看到各种绑定都达到了预期的结果

* bind -- 每次通过make实例化新的对象

* singleton -- 注册单例，不言而喻

* instance -- 通过已经实例化的对戏注册单例

---

# Container Usage

顺藤摸瓜，看 Container.php 这个类，首先

    use Closure;

这个匿名函数类在后面很多地方用到，不熟悉的可以去看一下官网的介绍 [Closure](http://php.net/manual/zh/class.closure.php)

    use ArrayAccess;

数组式访问接口，在 laravel 中很多地方都使用这个接口，[ArrayAccess](http://php.net/manual/zh/class.arrayaccess.php)，定义好其中的四个方法就能像访问数组一样访问对象了。

    $greet = new Acme\Greet;
    $container['greet'] = $greet;
    var_dump(isset($container['greet'])); // true
    var_dump($container['greet']);        // object(Acme\Greet) ..
    unset($container['greet']);
    try {
        var_dump($container['greet']);
    } catch(Exception $e) {
        print $e->getMessage();           // Class greet does not exist
    }

以上代码访问到了 Container.php 的四个 offset... 方法，简单的解释一下

    /**
     * 判断 bindings[$key] 是否存在。使用形式：isset($container['greet'])
     */
    public function offsetExists($key)
    {
        return isset($this->bindings[$key]);
    }

    /**
     * 实例化对象，调用 make() 方法，使用形式：$container['greet']
     */
    public function offsetGet($key)
    {
        return $this->make($key);
    }

    /**
     * 如果 $value 是一个闭包函数，就直接绑定
     * $container['test'] = function() {
     *     echo 'this is a test';
     * };
     * 如果 $value 不是闭包函数，就先将 $value 重新赋值为一个闭包
     * 注册对象到容器，$container['foo', $foo] 等同 $container->bind('foo', 'Acme\Foo')
     */
    public function offsetSet($key, $value)
    {
        if (! $value instanceof Closure) {
            $value = function () use ($value) {
                return $value;
            };
        }

        $this->bind($key, $value);
    }

    /**
     * 同 offsetExists()
     * 注意：会同时释放 bindings[$key], $instances[$key], $resolved[$key]
     * 因此调用 unset() 之后，就不能以任何形式从容器实例化对象了
     */
    public function offsetUnset($key)
    {
        unset($this->bindings[$key], $this->instances[$key], $this->resolved[$key]);
    }

---

# Container 核心

接下来会涉及到 Container 中的核心属性和方法

    use ReflectionClass;

使用反射类实例化容器之中的对象。

    /**
     * 注册类的别名数组集合
     */
    protected $aliases = [];

    public function bind($abstract, $concrete = null, $shared = false)
    {
        /**
         * 如果$abstract为数组
         * 更多细节请看 extractAlias() 函数 和 alias() 函数怎么分解数组
         * $container->bind(['foo', 'greet'], 'Acme\Foo');
         * $container->bind(['greet', 'foo'], 'Acme\Greet');
         * var_dump($container);
         * 输出 array(2) { ["foo"] => int(0) ["greet"]=> int(0) }
         * 最后赋值到 aliases 数组
         */
        if (is_array($abstract)) {
            list($abstract, $alias) = $this->extractAlias($abstract);

            $this->alias($abstract, $alias);
        }

        /**
         * 释放 $instances $aliases 中键为 $abstract 的元素
         */
        $this->dropStaleInstances($abstract);

        /**
         * 如果不传实体参数，例如 $container->make('foo')
         */
        if (is_null($concrete)) {
            $concrete = $abstract;
        }

        /**
         * $concrete 不是闭包函数，创建一个闭包，不过 getClosure() 这个方法会根据 $concrete 和 $abstract 是否相同来返回不同的闭包. build() | make()
         */
        if (! $concrete instanceof Closure) {
            $concrete = $this->getClosure($abstract, $concrete);
        }

        /**
         * 将返回的 $concrete, $shared 赋值到 $bindings[$abstract] 上
         */
        $this->bindings[$abstract] = compact('concrete', 'shared');

        /**
         * 如果该构造类已经被解析(例如 'foo')，即存在于$resolved 或 $instances 数组中
         * 执行 rebound()，查找 $reboundCallbacks 中存在方法并调用
         */
        if ($this->resolved($abstract)) {
            $this->rebound($abstract);
        }
    }

接着可以去看 bindShared(), build(), make() 这两个重要的方法，基本上 container 中其余的方法也会涵盖到了。例如 build() 方法是这样来创建实例的

    public function build($concrete, array $parameters = [])
    {
        /**
         * 当 $concrete 为闭包，很简单，直接执行这个方法。
         */
        if ($concrete instanceof Closure) {
            return $concrete($this, $parameters);
        }

        /**
         * 先构建它的反射类
         */
        $reflector = new ReflectionClass($concrete);

        /**
         * 当该类是抽象类或者借口时，抛出异常 (请看下面的例子1)
         */
        if (! $reflector->isInstantiable()) {
            $message = "Target [$concrete] is not instantiable.";

            throw new BindingResolutionContractException($message);
        }

        /**
         * 将实体压入栈底
         */
        $this->buildStack[] = $concrete;

        /**
         * 获取类的构造器
         */
        $constructor = $reflector->getConstructor();

        /**
         * 没有构造器，从栈类弹出刚才压入的实体，并且实例化这个实体类
         */
        if (is_null($constructor)) {
            array_pop($this->buildStack);

            return new $concrete;
        }

        /**
         * 获取构造器参数构成的数组
         */
        $dependencies = $constructor->getParameters();

        /**
         * 不详(:)) (去掉了也可以，laravel5 之前是没有这个方法的)
         */
        $parameters = $this->keyParametersByArgument(
            $dependencies, $parameters
        );

        /**
         * 获取依赖的实例对象，之中又有多种情况
         */
        $instances = $this->getDependencies(
            $dependencies, $parameters
        );

        /**
         * 弹出栈底实体
         */
        array_pop($this->buildStack);

        /**
         * 实例化传入类实体的实体对象 (即 $concrete)
         */
        return $reflector->newInstanceArgs($instances);
    }

# 例子

我们的例子是构建在一个 laravel 项目之中的。

例一，不注册实体

    // routes.php
    // Target [BarInterface] is not instantiable. 抛出 BindingResolutionException 异常 (在 build 方法中定义，即不能实例化)

    Interface BarInterface{}

    get('bar', function(BarInterface $bar) {
        dd($bar);
    });

例二，绑定

    // routes.php

    Interface BarInterface{}
    class Bar implements BarInterface{}

    app()->bind('BarInterface', 'Bar');

    get('bar', function(BarInterface $bar) {
        dd($bar);
    });

例三，加上依赖和参数(有默认值)

    // routes.php

    Interface BarInterface{}
    class Bar implements BarInterface{
        protected $baz;
        protected $foo;

        public function __construct (Baz $baz, $foo = 'foo')
        {
            $this->baz = $baz;
            $this->baz = $foo;
        }
    }
    class Baz {}

    app()->bind('BarInterface', 'Bar');

    get('bar', function(BarInterface $bar) {
        dd($bar);
    });

例四，参数(没有默认值)

    // 这个时候又会抛出一个 BindingResolutionContractException 异常，在 resolveNonClass 中定义

    public function __construct (Baz $baz, $foo)

---

#总结

laravel的IoC，Service Provider 比较底层的内容就介绍完了，当然还有很多函数没有介绍，这只是比较重要的一部分，如果你还是比较模糊，那就来自己写一个简单的 IoC 理解理解，仿照 laravel

---

# 补充

差不多还是开始的那个目录结构。。。

    // Foo.php
    <?php

    namespace Acme;

    class Foo
    {
        protected $greet;
        protected $foo;

        public function __construct(Greet $greet, $foo = 'foo')
        {
            $this->greet = $greet;
            $this->foo = $foo;
        }

        public function bar()
        {
            $this->greet->sayHelloTo();
        }
    }

    // index.php
    <?php

    require 'vendor/autoload.php';

    class AutoInstance
    {
        public function build($className)
        {
            if ($className instanceof Closure) {
                return $className();
            }
            // 创建反射类
            $reflector = new ReflectionClass($className);
            // 不能实例化，抛异常
            if (! $reflector->isInstantiable()) {
                $message = "Target [$className] is not instantiable.";

                throw new Exception($message);
            }
            // 创建构造器
            $constructor = $reflector->getConstructor();
            // 没有构造函数
            if ($constructor === null) {
                return new $className;
            }
            // 获取构造函数参数
            $dependencies = $constructor->getParameters();
            // 获取依赖实例对象
            $instances = $this->getDependencies($dependencies);
            // 实例化对象
            return $reflector->newInstanceArgs($instances);
        }

        protected function getDependencies($parameters)
        {
            $dependencies = [];

            foreach ($parameters as $parameter) {
                $dependency = $parameter->getClass();

                if ($dependency === null) {
                    $dependencies[] = $this->resolveNonClass($parameter);
                } else {
                    $dependencies[] = $this->build($dependency->name);
                }
            }

            return (array) $dependencies;
        }

        protected function resolveNonClass($parameter)
        {
            if(! $parameter->isDefaultValueAvailable()) {
                $message = "Unresolvable dependency resolving [$parameter] in class {$parameter->getDeclaringClass()->getName()}";

                throw new Exception($message);
            }

            return $parameter->getDefaultValue();
        }
    }

    var_dump((new AutoInstance)->build('Acme\Greet'));
    $foo = (new AutoInstance)->build('Acme\Foo');
    $foo->bar('RryLee');

PS: 有不足或者错误的欢迎指证，文章欢迎转载(都懂得)
