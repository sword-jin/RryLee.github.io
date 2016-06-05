---
layout: post
title: "开发环境配置清单总结(非 mac)"
subtitle:   ""
date:   2016-06-01
author: "RryLee"
tags:
    - 环境
---

* content
{:toc}

> 第一次发简书，总结下大学的开发环境，因为看到大部分人没有这些环境，所以给个参考，毕竟，工具还是很重要的。

# windows 下的几个工具

虽然好久没用 windows 了，不过还记得当初几个比较喜欢的软件。

* [cmder](https://github.com/cmderdev/cmder)

windows 下最让人不堪的就是命令行工具了，使用 cmder 可以让你的命令行基本可以和 linux 下的 terminal 同样爽，甚至更加好用。

* [wget](http://www.interlog.com/~tcharron/wgetwin.html)

wget 工具，windows 下有时候还是需要的

* [Internet Download Manager](http://www.internetdownloadmanager.com/) 

windows 下最喜欢的工具，linux 下至今没有寻找到可以媲美的工具。

* [WhatPluse](https://whatpulse.org/) 

全程记录键盘使用，两年后可能会有 sublime 100000000 次点击，当然，还有其他功能

# 代码工具

我使用 (sublime(多) + phpstorm(少)) 写后端代码，atom 写前端代码

下面安利出我的插件

* sublime
    
    * [AdvancedNewFile](https://packagecontrol.io/packages/AdvancedNewFile) - 快速新建文件

    > { "keys": ["ctrl+n"], "command": "advanced_new_file_new"} // 绑定新建文件快捷键
    
    * [All Autocomplete](https://packagecontrol.io/packages/All%20Autocomplete) - 代码补全(大概，这个插件一直都在)
    
    * [Autoprefixer css](https://packagecontrol.io/packages/Autoprefixer) - 浏览器兼容前缀补全
    
    * [Blade Snippets](https://packagecontrol.io/packages/Blade%20Snippets) - 代码片段(laravel blade)
    
    * [Bootstrap 3 Snippets](https://packagecontrol.io/packages/Bootstrap%203%20Snippets) - 同上
    
    * [BracketHighlighter](https://packagecontrol.io/packages/BracketHighlighter) - 必装的样子 XD
    
    * [Color Highlighter](https://packagecontrol.io/packages/Color%20Highlighter) - 显示颜色辅助工具，很方便的
    
    * [Colorsublime](https://packagecontrol.io/packages/Colorsublime) - 预览安装 sublime 的全部配置方案
    
    * [DocBlockr](https://packagecontrol.io/packages/DocBlockr) - 代码注释工具
    
    * [Emmet](https://packagecontrol.io/packages/Emmet) - 都知道的
    
    * [Git](https://packagecontrol.io/packages/Git) - 很方便的，不过我用的少
    
    * [Highlighter](https://packagecontrol.io/packages/Highlight) - 代码高亮工具，必装的
    
    * [JavaScriptNect - ES6 Syntax](https://packagecontrol.io/packages/JavaScriptNext%20-%20ES6%20Syntax) 让你的js es6 代码支持的更好
    
    * [JSX](https://packagecontrol.io/packages/JSX) - 不用 React 可以跳过
    
    * [Laravel 5 Artisan](https://packagecontrol.io/packages/Laravel%205%20Artisan) - laravel 的神器
    
    * [Laravel Blade Highlighter](https://packagecontrol.io/packages/Laravel%20Blade%20Highlighter) - laravel blade 语法高亮
    
    * [Markdown Preview](https://packagecontrol.io/packages/Markdown%20Preview) - 生产 md　的 html 文件，有些时候很有用（打印文档）
    
    * [PHP Companion](https://packagecontrol.io/packages/PHP%20Companion) - php 函数跳转，命名空间处理神器　（我的改建）
    
    ```json
    { "keys": ["f6"], "command": "expand_fqcn" },
    { "keys": ["shift+f6"], "command": "expand_fqcn", "args": {"leading_separator": true} },
    { "keys": ["f5"], "command": "find_use" },
    { "keys": ["f4"], "command": "import_namespace" },
    { "keys": ["f12"], "command": "goto_definition_scope" },
    { "keys": ["ctrl+1"], "command": "insert_php_constructor_property" }
    ```    

    * [PHP Getters and Setter](https://packagecontrol.io/packages/PHP%20Getters%20and%20Setters) - 属性的get set代码生成，有时候还可以
    
    * Sass Scss stylus - 语法高亮支持
    
    * [SideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements) - 必装，filetree
    
    * [SublimeCodeIntel](https://packagecontrol.io/packages/SublimeCodeIntel) - 补全有关
    
    * [Terminal](https://packagecontrol.io/packages/Terminal) - 快速打开终端，以前 windows 下最常用的
    
    * [TrailingSpaces](https://packagecontrol.io/packages/TrailingSpaces) - 帮助取消空格
    
    * Twig, Vue Syntax Highlight - Twig, Vue 语法高亮
    
    * [WakaTime](https://packagecontrol.io/packages/WakaTime) - WakaTime 的官方插件, 使用 WakaTime 记录编码时间，自行 Google.
    
* Sublime 主题
    * [Material Theme](https://packagecontrol.io/packages/Material%20Theme) (脑补，XD)
    
    * [Boxy Theme](https://packagecontrol.io/packages/Boxy%20Theme)
    ![](http://ww2.sinaimg.cn/large/74311666jw1f4kso82nkyj20w00j8td9.jpg)

* Atom (和 sublime 基本差不多)
    
    * [Stylus](https://atom.io/packages/stylus)
    
    * [advanced-open-file](https://atom.io/packages/advanced-open-file)

    * atom-autocomplete-php, autocomplete-php, autocomplete-modules, autocomplete-python
    
    * [atom-runner](https://atom.io/packages/atom-runner) - 自定义任务（类似 sublime 中的 ctrl + b）
    
    * [atom-terminal](https://atom.io/packages/atom-terminal) - 超级强大的 atom 命令行，调用系统默认(zsh, bash, fish)
    
    * [emmet](https://atom.io/packages/emmet)
    
    * [docblockr](https://atom.io/packages/docblockr)
    
    * [file-icons](https://atom.io/packages/file-icons) - 文件图标必备
    
    * [highlight-selected](https://atom.io/packages/highlight-selected) - 高亮选中单词的其他全部

    * language-javascript-jsx, react-snippet - react 必备
    
    * [language-vue](https://atom.io/packages/language-vue) - vue 高亮
    
    * [linter](https://atom.io/packages/linter), [linter-selint](https://atom.io/packages/linter-eslint), [linter-php](https://atom.io/packages/linter-php) - 不多说，atom 的一大亮点
    
    * [project-manager](https://atom.io/packages/project-manager) - 强大的项目管理器，保存好之后，随时快速查看之前的项目代码
    
    * [simple-drag-drop-text](https://atom.io/packages/simple-drag-drop-text) - 鼠标拖拉移动代码
    
    * [tokamak-terminal](https://atom.io/packages/tokamak-terminal) - 依赖之前的 terminal,目前使用的
    
    * [tool-bar](https://atom.io/packages/tool-bar), [tool-bar-main](https://atom.io/packages/tool-bar-main) - 和 [vscode](https://code.visualstudio.com/) 一样的一个工具栏
    
    * [wakatime](https://atom.io/packages/wakatime)
    
* atom 主题颜色
    
    * Atom Material 主题
    
    * Nucleus Dark(ing) 主题
    ![](http://ww2.sinaimg.cn/large/74311666jw1f4ksnmt47cj20yg0i1gok.jpg)

    * atom-material-ui 配色
    
    * [adventurous-syntax](https://atom.io/packages/adventurous-syntax)
    
    * [batman-syntax](https://atom.io/packages/batman-syntax)
    
    > 目前使用 Nucleus Dark + atom-material-ui

# 开发环境

* virtualbox - 虚拟机
    
* vagrant - 打包集成虚拟环境
    
> 参考视频 (http://learner.video/series/php-tips-and-tricks/episodes/14)，博客 (http://rrylee.github.io/2015/11/26/quick-start-linux-php/)

* Homestead - laravel 项目开发环境

* nvm - node 版本管理工具，安装也非常方便，github -> nvm

* terminator - 命令行工具，参考 https://github.com/RryLee/.dotfiles/blob/master/terminator/config

* fish + oh-my-fish - 配置参考 https://github.com/RryLee/.dotfiles/blob/master/fish/config.fish

* php

```plain
php -v // PHP 7.0.7-2
```

* mysql

```plain
mysql -V // 5.6.30
```

# linux 几个软件

* lantern - 梯子, github 见

* shutter - 截图

* musicbox - 网易云音乐命令行版本

* you-get - 视频媒体下载，基本网络国内外大部分网站

* webtorrent-desktop - 种子文件下载, github 下载

* vuze - linux 下好用的挂种工具，可以上蝴蝶Bt

* steam - 别忘了 steam　下好多 linux 游戏，高手请联系我

* skype, gitter, slack - 聊天工具

# linux 桌面

* gnome3 - 配置好推荐，在不开发插件的情况下，靠自己 dir 功能不多。目前电脑配置有限，会卡。
* i3wm - 个人觉得最酷的桌面，可以随意 dir，但是开发 electron 这种桌面应用有点蛋疼，不知道怎么解决。**关键是超级流畅**
* budgie - 和 i3wm 换着使用
* xfce4 - 最近准备转战 archlinux，正在学习，感觉非常酷，可玩性很高。

# 浏览器

* chrome

    * AdBlock - 就在那，不管好不好用
    
    * Avatars for Github - github 用户名显示图像，很好用
    
    * Chrome Sniffer Plus - 查看网站开源库的使用
    
    * ColorZilla - 取色工具
    
    * DevTools Author - 控制台修改工具
    
    * Dream Afar New Tab - 好看的 tab 页面
    
    * EditThisCookie - 不言而喻
    
    * Gliffy Diagrams - 简单的作图工具
    
    * Isometric Contributions - 像建楼一样查看 github 贡献
    
    * JSON Formatter - 查看 Json 的实用工具
    
    * LastPass: Free Password Manager - 密码管理工具（个人觉得自己账号没啥价值，所以基本全部使用lastpass管理）
    
    * OneTab - 保存当前所有 tab
    
    * Pocket, SaveToPocket, Wunderlist for Chrome - 几个推荐 app
    
    * Postman - api 调试神器
    
    * React Developer Tools, Vuejs DevTools, Vulcan by Firebase - react, vue, firebas 调试工具
    
    * Web Paint - 在当前页面图画工具
    
    * 划词翻译 - 超好用的词典
    
    * Vimium - vim 党的胜利
    
    * 易集书签收藏工具 - 使用易集的同学，这是个好用的工具

* chrome 主题 - arc-theme

* firefox

和 chrome 基本保持着同样的工具链，只是更喜欢在 chrome 里面，话说 firefox 的 arc-theme，下载安装 https://github.com/horst3180/arc-firefox-theme/releases

> 基本常用的都记录了，有补充的保持更新。文章每个插件功能都没有配图，不打算配图，根据描述可以 google 了解。
