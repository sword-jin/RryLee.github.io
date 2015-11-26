---
layout: post
title: "使用vagrant轻松搭建linux下的php开发环境"
date:   2015-11-26 11:00
categories: 环境
excerpt: RryTip 博客 视频 上线
---

* content
{:toc}

> 有视频参考 [link](http://rrytip.github.io/home/php-tips/build-linux-php-enviroment-with-homestead.html)

# 闲扯

在这个 Docker 越发火热的时代，你是否还在windows下的wamp或者xampp环境下进行php开发，这样未免显得太low，而且windows和实际生产环节也会有较大的区别，那么，何不转移到linux下，vagrant来搭建一个php环境不要太简单。So

# 准备

你还是先需要装这两个基本的环境的

* [Vm VirtualBox](https://www.virtualbox.org/ ) 比较推荐, 下载安装最新版

* [Vagrant](https://www.vagrantup.com/)

功能就不再赘述了，使用laravel的同学没有在homestead开发的可以跳转 [Windows下Homestead环境部署](http://rrylee.github.io/2015/07/25/Homestead%E6%90%AD%E5%BB%BA/)。

# 开始

安装好以上两个环境之后 `vagrant -v` 查看vagratn版本来检查是否安装成功!

接下来初始化一个 vagrant 配置文件，新建一个 learn-vagrant 文件夹，进入

`vagrant init`

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1eye7zyr5hzj20k407x0ue.jpg)

编辑 Vagrantfile 这个文件

    第15行 => config.vm.box = "ubuntu/trusty64" 使用这个ubuntu

    第25行去掉注释

这样就简单的配置好了一个vagrant，接来下去启动这个虚拟环境。

`vagrant up`

> 这个时候会自动去下载一个 .box 文件，里面配置了虚拟机，根据经验，这套系统的下载速度在国内还是相当不错的(稳定600k左右).

估计补到十分钟吧，下载完成了的样子，接下来使用 ssh 登录到虚拟机里面，关于ssh，请参考之前的博客，用 `git bash` 产生的。

`vagrant ssh` 进入ubuntu.

我们的命令行会有所改变，可以看到

![](http://ww3.sinaimg.cn/mw690/baa3278fgw1eye85cc1goj20hu019q2r.jpg)

老样子，第一件事情 `sudo apt-get update` 来升级系统的一些包.

之后就开始安装 mysql apache 吧。

`sudo apt-get install apache2 php5 libapache2-mod-php5`

安装好之后 `php -v` 应该会打印出当前php版本信息

这个时候在浏览器中 http://localhost:8080/，又可以看到熟悉的画面了。

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1eye7zyr5hzj20k407x0ue.jpg)

安装 mysql

`sudo apt-get install mysql-server mysql-client php5-mysql`

中间时候会需要你输入 mysql 客户端用户名密码的，随意填写就好

bingo `mysql --version` 成功打印

这个时候还需要我们去修改下 apache2 配置文件, `/etc/apache2/sites-enabled/000-default.conf`

    #修改 DocumentRoot
    DocumentRoot /vagrant
    #顺便加上
    <Directory /vagrant>
        Options -Indexes +followSymLinks +MultiViews
        AllowOverride all
        Require all granted
    </Directory>

`sudo service apache2 restart`，重启之后在之前的learn-vagrant目录下新建 index.php

    <?php

    phpinfo();

再次访问 http://localhost:8080 就看到更加熟悉的画面了，这里就不放图了，相信都没什么问题。

# 项目

这次我们来启动一个 laravel 项目

还是那个目录，删掉 index.php 这个测试文件，然后将laravel的项目文件都添加到目录下，接着去修改下根目录为laravel的public

    DocumentRoot /vagrant/public
    <Directory /vagrant/public>
        Options -Indexes +followSymLinks +MultiViews
        AllowOverride all
        Require all granted
    </Directory>

**重启服务器**再次访问 `http://localhost:8080`

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1eye9a9vninj20ur0l3q3n.jpg)

去到我们的 routes.php 文件中添加一个路由

    get('/home', function() {
        return 'Hello, RryTip';
    });

现在你访问 `http://localhost:8080/home` 是没有结果的，你需要开启apache Rewrite 模块, So

`sudo a2enmod rewrite`

接着重启服务器之后访问 http://localhost:8080/home 既可以看到 Hello, RryTip 了!

# 总结

这种搭建一个开发环境是很方便的，不用太多的考虑，因为，一个 vagrant 的销毁也是如此简单

`vagrant box list` 就可以看到刚才添加的那个box了

看图->

![](http://ww2.sinaimg.cn/mw690/baa3278fgw1eye9j2y53kj20m806rjst.jpg)

然后去 C 盘用户目录下的

`\.vagrant.d\boxes\ubuntu-VAGRANTSLASH-trusty64`

`\VirtualBox VMs\learn-vagrant_default_1448508213520_63818`

删除这两个文件夹就 ok 了!

感谢围观!
