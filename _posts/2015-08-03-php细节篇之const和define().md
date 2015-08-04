---
layout: post
title: "php细节篇之const与define()"
---

这个系列开始探寻 php 的各种语句和函数，下面是 const 和 define() 的一些用法。

在 php 中你可以使用 define() 和 const 来定义常量

    define('NAME', 'RryLee');
    const NAME = 'RryLee';

两者最大的不同 const 编译常量在编译期间，而 define 定义常量在运行期间。这就造成了 const 的一些缺点：

const 不能在条件语句内使用

    if (...) {
        const FOO = 'BAR';   // invalid
    }

    if (...) {
        define('FOO', 'BAR');
    }

所以我们可以通过这样来检查常量是否被定义

    if (! define('FOO')) {
        define('FOO', 'BAR');
    }

const 可以接受一个静态标量 (number, string, true, false, null, __FILE__ ), define() 可以接受任何表达式，在 php5.6之后const也可以接受常量表达式了。

    const BIT_5 = 1 << 5;   //  valid since php5.6
    define('BIT_5', 1<<5);  // valid

const 必须要使用常量作为名称(一般默认大写), define()可以接受任何表达式作为name

    for ($i=0;$i<32;$i++) {
        define('BIT_' . $i, 1 << $i);
    }

const 的大小写是很严格的，但是 define() 可以通过传递 true 参数来定义常量

    define('FOO', 'BAR', true);
    echo FOO;  // BAR
    echo foo;  // BAR

const定义常量使用当前命名空间，而define() 需要使用完整的命名空间

    namespace A\B\C;
    const FOO1 = 'BAR1';
    define('A\B\C\FOO2', 'BAR2');

自从php5.6 之后 const 能定义数组，而define()在php7.0之后开始支持

    const FOO = [1,2,3];
    define('FOO', [1,2,3]);

最后，const可以在类中使用，很常见的做法，而define()是不被允许的。

    class Foo
    {
        const BAR = 3;  // valid
    }
    class Baz
    {
        define('QUX', 2);  // invalid
    }

总结，一般来说定义一些全局常量我们会使用 define() ，然而一些类中的局部常量通常使用 const.
