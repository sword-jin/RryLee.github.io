---
layout: post
title: "HTML5文件上传后篇--文件分割。。。"
date:   2015-08-20 16:00
categories: HTML
excerpt: 使用HTML5特性完成大文件分割上传，图片文件显示，拖拽上传
---

HTML5 文件上传部分还有以下几个重要的特性：

* content
{:toc}

## 图片文件显示

我们可以看到 `Bom` 的 `URL` 下有这样一个 [createObjectURL](https://w3c.github.io/FileAPI/#dfn-createObjectURL) 方法，是干嘛用的，返回一个唯一的 [Blob URL](https://w3c.github.io/FileAPI/#DefinitionOfScheme)，大概意思就是说一个二进制字符串，这个字符串可被浏览器解析直接输出，至于图片格式，就能呈现出来。

> 以上是我的理解，有兴趣的同学自行查看文档。

    ...
    <!-- css -->
        img {
            width: 400px;
            height: 400px;
        }
    ...
    <h1>图片上传显示</h1>
    <input type="file" name="file" onchange="send();">
    <div id="debug"></div>

    <!-- js -->
        function send() {
            var fd = new FormData();

            var file = document.getElementsByTagName('input')[0].files[0];

            fd.append('file', file);

            var xhr = new XMLHttpRequest;

            xhr.open('POST', 'up.php', true);

            xhr.send(fd);
            // 将图片资源转化为二进制显示
            var tmpimg = document.createElement('img');
            tmpimg.src = window.URL.createObjectURL(file);
            document.getElementsByTagName('body')[0].appendChild(tmpimg);
        }

我们的后台处理文件还是之前那样，做一个存储功能。

## 文件切割上传

我们知道一般后台文件上传的大小是需要配置的，一般 php.ini 中的 `upload_max_filesize` 默认配置大概是 2M 吧，这里我们需要调整以下(假设为25M)。文件切割上传有两个点需要注意：

* 后台程序需要不停的向后添加数据
* 前台不停的发起请求

下面请看 demo：

    // up.php
    if (! file_exists('./up/up.mp4')) { // 文件不存在则直接转移文件

    move_uploaded_file($_FILES['part']['tmp_name'], './up/up.mp4');

    } else { // 存在，将上传的部分写入已经存在文件的后面

        file_put_contents("./up/up.mp4", file_get_contents($_FILES['part']['tmp_name']), FILE_APPEND);
    }

    // index.html
    <!-- css -->
    ...
        #progress {
            width: 500px;
            height: 20px;
        }
        #bar {
            color: white;
            width: 0%;
            height: 100%;
            background: #5cb85c;
        }
    ...
    <h1>文件上传效果</h1>
    <div id="progress">
        <div id="bar"></div>
    </div>
    <input type="file" name="file" onchange="upload();">

    <!-- JS -->
        var xhr = new XMLHttpRequest();
        var clock = null; // 定时器，负责 http request 的开启和停止

        /**
         * ajax上传
         * @param  array fd post数据
         */
        function up (fd) {
            xhr.open('POST', 'up1.php', false);
            xhr.send(fd);
        }

        /**
         * 定时上传
         */
        function upload() {
            clock = window.setInterval(sendfile,1000);
        }

        // 一个文件上传的闭包
        var sendfile = (function() {
            const LENGTH = 10 * 1024 * 1024; // 固定每次上传字节 10M
            var start = 0;                   // 开始上传文件的字节处
            var end = start + LENGTH;        // 文件结束字节处
            var sending = true;              // 上传标志 true: ing  false: end

            var part = null;                 // 每次上传文件
            var fb = null;                   // FormData Object

            var percent = 0;                 // 百分比

            // 执行闭包
            return (function() {
                // 每次上传前都要检测是否运行上传
                if (! sending) {
                    clearInterval(clock);
                    return;
                }
                var file = document.getElementsByName('file')[0].files[0];
                // 上传完毕
                if (start > file.size) {
                    clearInterval(clock);
                    return;
                }
                // 切割文件
                part = file.slice(start, end);
                fd = new FormData();
                fd.append('part', part);
                up(fd); // 上传

                start = end;
                end = start + LENGTH;
                sending = true;

                percent = 100 * end / file.size;
                if (percent > 100) {
                    percent = 100;
                }

                document.getElementById('bar').style.width = percent + '%';
                document.getElementById('bar').innerHTML = parseInt(percent) + '%';
            });
        })();

这里又使用了一个 [slice](https://w3c.github.io/FileAPI/#slice-method-algo) 方法，每一个文件对象的原型链上都可以找到 File 对象，File 对象的原型链上又能找到一个 `slice` 方法，也就是专门用来切割文件的。

![](http://ww3.sinaimg.cn/mw690/baa3278fgw1ev96d0o0vrj20ei0a83zr.jpg)

以上就是大文件分割上传

---

## 拖拽上传

主要是使用了dragover、dragend和drop这三个事件.

    <!-- css -->
    ...
        #container {
            border: 4px dashed #EEE;
            width: 400px;
            min-height: 300px;
            margin: 20px auto;
        }
        #container.hover {
            border: 8px dashed #8b869e;
        }
    ...

    <div id="container"></div>

    <!-- Js -->
        if('draggable' in document.createElement('span')){
    　　　　var container = document.getElementById('container');
    　　　　container.ondragover = function() { <!-- 文件进入（被拖拽） -->
                this.className = 'hover';
                return false;
            };
    　　　　container.ondragend = function() { <!-- 拖拽完成 -->
                this.className = '';
                return false;
            };
    　　　　container.ondrop = function(event) { <!-- 整个上传过程 -->
    　　　　　　event.preventDefault();
    　　　　　　this.className = '';
    　　　　　　var files = event.dataTransfer.files;
    　　　　　　// 后面的就跟之前的上传步骤大同小异了
    　　　　};
    　　}

---

总结：利用 HTML5 新特性完成的文件上传告一段落！
