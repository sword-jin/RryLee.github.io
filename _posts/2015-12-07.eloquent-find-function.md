---
layout: post
title: "看看Eloquent中的find方法"
date:   2015-12-07 17:00
categories: laravel
excerpt: laravel eloquent find function
---

* content
{:toc}

在我们的 `User` 模型中写上这样一个方法

    public static function getUserById($id)
    {
        return self::find($id);
    }

在我们的 `routes.php` 去是调用以下这个方法

    get('/', function () {
        return App\User::getPostById(1);
    });

能得到你想要的结果。

如果你使用 IDE，你会发现找不到我们 `Model` 中的 `find` 方法，但是我记得在 `laravel5.1` 之前一直是存在的，忍不住看了一下 5.1 的 5.0 的代码，大概猜到了可能是我们的 `eloquent` 更新了。

# 5.0

很简单的就找到了我们的 find 方法

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1eyr76tm7yij20ld0esgms.jpg)

这里细节先留在，在 5.1 的版本中来看看，因为区别几乎没有。

# 5.1

既然在5.1的 `model` 中没有找到 `find()` 这个方法，然后在具体模型又可以去调用这个方法，想必只有一个可能吧

**__call()__** 和 **__callStatic()__** 这个两个魔术方法

那我们在 `model` 中去找到 `__call()`, `__callStatic()`

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1eyr7dn0uzjj20jc0ez0u2.jpg)

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1eyrggn2sjuj20hk0d5dgj.jpg)

可以看到，当触发 `__callStatic()` 是会去触发 `__call()`

> 原来我们使用的 `User::find(1)` `$user = new User;$user->find(1)` 就来自于此

如果你愿意，在`__call()` 的 `return` 之前随便 `dd` 以下，然后调用我们的 `getUserById()` 肯定是会断点的。

`increment()` 和 `decrement()` 就不用多说，只是对某个字段进行加减

> 不过这边这两个方法为什么使用魔术方法来调用就不太清楚了，可以讨论一下。

接着看 `newQuery()` 这个方法

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1eyr7talyxsj20ee09jt9b.jpg)

`call_user_func_array([$query, $method], $parameters) ` 我觉得通过这段代码你都能猜到是调用 $query 这个对象的 `find()` 方法了。

接着是 `newQueryWithoutScopes()` 这个方法怎么去实例化我们的 `query`

![](http://ww3.sinaimg.cn/mw690/baa3278fgw1eyr7xpjk9fj20mr0dqmyl.jpg)

    $builder = $this->newEloquentBuilder(
        $this->newBaseQueryBuilder()
    );

在我们的 `newEloquentBuilder()` 方法传递一个新的 builder(这个builder与我们的数据库操作层紧密耦合)

    /**
     * Create a new Eloquent query builder for the model.
     *
     * @param  \Illuminate\Database\Query\Builder  $query
     * @return \Illuminate\Database\Eloquent\Builder|static
     */
    public function newEloquentBuilder($query)
    {
        return new Builder($query);
    }

传递什么参数，返回什么对象注释都写得很清楚，本着探索的精神还是接着看下去。

Illuminate\Database\Eloquent\Builder 的构造方法

    public function __construct(QueryBuilder $query)
    {
        $this->query = $query;
    }

接来下看如何拿到这个 `QueryBuilder` 的对象，我们之前的 `newBaseQueryBuilder()`

![](http://ww4.sinaimg.cn/mw690/baa3278fgw1eyr87h5gr2j20ki0aw3zd.jpg)

中间还是封装了很多细节，连起来就是

* Query\Builder 封装了底层的数据库操作

* Eloquent\Builder 依赖上面的 Builder 封装了一系列非常好用的方法

最后你可以看到我们的 `Eloquent\Builder` 的 `find()`

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1eyrfo08un6j20om0hb0tz.jpg)

这样看就比较简单了，调用 `model` 的 `getQualifiedKeyName()` 拿到主键，然后使用 `__call` 来调用 `where()` 去查询数据库...

# 结论

我们的 `find()` 方法只是被简单的换到了 `Eloquent\Builder` 这个类中。

如果你多看一些的话，你会发现，和数据库操作更加耦合的一部分方法都被移到了 `Eloquent\Builder`，这大概也是 Eloquent 的一次重要升级吧。

> 能力有些，有不好的错误的还请指正。

