---
layout: post
title: "Windows下Homestead环境部署"
---
最近因为一些原因需要转移到 `Homestead` 上开发，折腾了差不多一天，Windows下总算是装部署好了 `Homestead`，总体来说，windows下相比 mac/linux 安装要复杂那么一点点。想要安装的同学也不用慌，坑也被我踩的差不多了，只需要跟着下面一步步的做就可以了。

下面是我详细的安装过程：

硬件：

    Window7/Window8

软件：

    VirtualBox
    Vagrant
    Laravel Homestead
    Git

首先安装好 `VirtualBox` 和 `Vagrant`，安装完成 `Vagrant` 后需要重启，重启后执行

    vagrant -v

![vagrant](http://img1.ph.126.net/-IE7iADM6vjE3mfi1_WG6w==/6630556492793064871.jpg)

说明安装完成，提示找不到命令的把 `vagrant` 的bin目录加入环境变量即可。

接下来你需要执行 `vagrant box add laravel/homestead` 来安装 homestead 的虚拟机文件，但是以目前来看，国内这样做是下载不来的，这是下载链接

    https://atlas.hashicorp.com/laravel/boxes/homestead/versions/0.2.6/providers/virtualbox.box

使用下载工具搞定后，就需要安装了，在同一目录下执行

   vagrant box add laravel/homestead ./homestead-0-2-6-vb.box

等待数秒即可安装完毕，add 后面两个参数，前面的自己随便取个名称，后面的是下载来的 `.box` 文件。

![](http://img2.ph.126.net/EwwV2RRXG0kyCVZzExDPow==/6619573471142949989.jpg)

接下来的操作需要用到 `git bash` 这个工具(下面全部命令都将在 bash 下执行)，下载安装好 `git` 之后打开 'git bash'，
随便找个目录下(这里假定在桌面)，执行

    git clone https://github.com/laravel/homestead.git Homestead

接在在 Homestead 下执行 bash init.sh

![bash](http://img2.ph.126.net/YCienEzGcraLMDUg-RyZ4Q==/6630916033095854879.jpg)

会在 C 盘生成一些 homestead 的配置文件，下面后修改这些配置。

在 C:\Users\li\.homestead 找到 Homestead.yaml，下面是我的配置

    ---
    ip: "192.168.10.10"  <!-- 虚拟机 ip -->
    memory: 2048
    cpus: 1
    provider: virtualbox

    authorize: ~/.ssh/id_rsa.pub

    keys:
        - ~/.ssh/id_rsa

    folders:
        - map: E:\Projects    <!-- 我的window项目地址 -->
          to: /home/vagrant/Code   <!-- 对应虚拟机的项目地址 -->

    sites:
        - map: laravel.dev  <!-- 添加的第一个站点名称 -->
          to: /home/vagrant/Code/laravel-dev/public <!-- 该站点对应的虚拟机文件 -->

    databases:
        - homestead

    variables:
        - key: APP_ENV
          value: local

现在配置 SSH ，也就是上面配置中看到 `~/.ssh/id_rsa.pub` 和 `~/.ssh/id_rsa`

在 git bash中运行 `ssh-keygen -t rsa -C "you@homestead"` ，这里填自己的邮箱就可以了。 接下里几个 `回车`，完成配置。

去我们的 E 盘中创建 Projects 文件夹，接着来我们的 Homestead 文件夹下运行 `vargrant init` 初始化，得到 Vagrantfile 文件。然后运行 `vagrant up`，第一次运行时会在 C 盘创建虚拟机文件，所以耐性等待，接下来看到我们的虚拟机启动成功。

![](http://img2.ph.126.net/WXpCQ7w3bm8KqLhN4T1Rnw==/6631318454349976754.jpg)

在浏览器中打开 `localhost:8000`，看到以下画面说明，homestead已经搭建成功。

![](http://img0.ph.126.net/XSKgCsCmZWXDEFWii14EkQ==/6619526192142954384.jpg)

接下来修改我们 `windows` 的host文件(不知道的同学自行百度)，在后面加上

    192.168.10.10     laravel.dev

然后访问可以看到同样画面。

这里要明白：配置中的

    folders:
        - map: E:\Projects    <!-- 我的window项目地址 -->
          to: /home/vagrant/Code   <!-- 对应虚拟机的项目地址 -->

就是将主机与虚拟的这个两个目录共享，虚拟机中的目录是站点目录，可以认为是我们之前所用的 www 目录和 htdocs 目录。好了，在 Projects中新建一个laravel项目名称为 laravel-dev (这里是为什么请自行理解)，然后在打开浏览器访问 laravel.dev，就是我们熟悉的画面了。

![](http://img2.ph.126.net/vkMvNVQJoa2XDVYn91vBmQ==/6630655448840072306.jpg)

接下来可以进入我们的虚拟机看看环境什么的。通过 `vagrant ssh` 进入到虚拟机

![](http://img1.ph.126.net/xP-bNldNsoh00eQkK3_3Bw==/6631426206489500026.jpg)

最后，安装参考这篇文章 [link](http://sherriflemings.blogspot.ca/2015/03/laravel-homestead-on-windows-8.html)，貌似国内进不去，需要参考的同学自行解决！

文章地址发表在我的[博客](http://rrylee.github.io/laravel/2015/07/25/Homestead搭建/)。



