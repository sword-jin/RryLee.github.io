---
layout: post
title: "使用 eslint 为团队或个人制定编码规范"
subtitle:   ""
date:   2016-05-19
author: "RryLee"
tags:
    - javascript
---

* content
{:toc}

# 关于

最近 js 代码写的比较多，有时候在代码中会纠结这里该用单引号还是双引号，是否添加分号，导致代码多处地方风格不太一致，这个时候，使用 eslint 可以节省很大功夫。

> Tip: 一般在 php 代码中，缩进为 4，但是前端代码中缩进一般为 2，这里推荐使用两套编辑器(ide)，我后端使用 [sublime](http://www.sublimetext.com/) / [phpstorm](https://www.jetbrains.com/phpstorm/)，前端 [atom](https://atom.io/) / [webstorm](https://www.jetbrains.com/webstorm/)，分别设置好默认缩进就 ok 了。

# eslint

你一定听说过 [jslint](http://www.jslint.com/)，不过你可能没使用过 [eslint](http://eslint.org/)。使用 eslint 来定制代码规范是很 cool 的(加上 atom)，eslint 完备的文档和第三方包也是非常方便的。

首先，在一个项目下安装 eslint

`npm install eslint --save-dev`

然后添加 `.eslintrc` 来配置 eslint，这里有以下规范：

* 缩进 2 格
* 不添加分号 `;`
* 字符串使用单引号 `'`

只需要配置

```json
{
  "rules": {
    "quotes": ["error", "single"],
    "indent": ["error", 2],
    "semi": [2, "never"]
  }
}
```

如果你使用 atom，并且安装了 [linter-eslint](https://atom.io/packages/linter-eslint) 插件，那么你可以在你项目的代码中看到这样的效果

![](http://i2.buimg.com/882065061153d3ae.png)

当然，sublime 貌似也有这样的插件 [Eslint](https://packagecontrol.io/packages/ESLint)，详情自行安装。

你也可以直接在项目下通过命令行调试

`node_modules/.bin/eslint .`

当然，这里是 eslint，当然要检测 es6 代码了，你需要安装 `babel-eslint`，
然后在 .eslintrc 中添加

`"parser": "babel-eslint"`，这样就可以规范 es6 的代码了。

> 你还可以使用各种第三方的规范包，比如 [eslint-config-standard](https://github.com/feross/eslint-config-standard)，安装配置文档中有。

接着愉快的写 js 代码了，详细强迫症的我们会自行调整不规范的代码。

