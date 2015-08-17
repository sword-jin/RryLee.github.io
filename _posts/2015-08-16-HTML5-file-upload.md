---
layout: post
title: "使用HTML5特性完成上传进度监控"
date:   2015-08-16 00:00
categories: HTML
excerpt: 使用HTML5特性完成上传进度监控
---

想知道文件插件是什么实现的，来初步探究它的原理。

假设有 JSON 和 PHP 的基础，文件的 POST 上是基础。首先我们来写一个 ajax 上传的例子

    <h1>文件上传效果</h1>
    <input type="file" name="file" onchange="send();">
    <div id="debug"></div>

    <script>
    function send() {
        var fd = new FormData();

        var file = document.getElementsByTagName('input')[0].files[0];

        fd.append('file', file);

        var xhr = new XMLHttpRequest;

        xhr.open('POST', 'up.php', true);

        xhr.onreadystatechange = function() {
            if (this.readyState == 4) {
                document.getElementById('debug').innerHTML = this.responseText;
            }
        }
        xhr.send(fd);
    }
    </script>

接下来使我们服务器端的代码

    // up.php
    if (empty($_FILES)) {
        exit('no file');
    }

    if ($_FILES['file']['error'] != 0) {
        exit('fail');
    }

    move_uploaded_file($_FILES['file']['tmp_name'], 'up/' . $_FILES['file']['name']);

    echo 'ok';

---

首先这里我们用到了 HTML5 中的 [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 对象，能很轻松的构建表单键值对，接着使用 AJAX 将数据 POST 服务器。

这样我们就简单的完成了一个文件上传的例子，但是还不够，我们需要能看到文件的上传进度，这里利用 HTML5 提供的
 [onprogress](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#Monitoring_progress) 事件来监控我们的文件上传。

修改我们你的 html 页面。

    ...
    <style>
        #progress {
            width: 500px;
            height: 20px;
        }
        #bar {
            width: 0%;
            height: 100%;
            background: #5cb85c;
        }
    </style>
    ...
    <h1>...</h1>
    <div id="progress">
        <div id="bar"></div>
    </div>
    ...
    // 上传图片进度
    xhr.upload.onprogress = function(e) {
        // console.log(e);  // 注意这里，我们可以在 CHROME 中调试
        if (e.lengthComputable) {
            var percent = e.loaded/e.total * 100;
            // console.log(percent);
            document.getElementById('bar').style.width = percent + '%';
            document.getElementById('bar').innerHTML = parseInt(percent) + '%';
        }
    }

![](http://ww1.sinaimg.cn/mw690/baa3278fgw1ev4pmghru6j20p907p75p.jpg)

---

onprogress 的返回的对象中有记录的上传的各种属性状态，这里我们需要这三个重要的属性值来完成对上传进度的监控。

总结，利用 HTML5 的特性来完成文件的上传是很方便的，日后也可能会成为各浏览器的标准，但是上传文件远不止这样，利用 AJAX 来分割大文件上传，拖拽上传，图片转化为2进制预览，所以，未完。。。

