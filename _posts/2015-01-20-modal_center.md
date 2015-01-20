---
title: 'Modal居中'
layout: post
tags:
    - tech
    - bootstrap
---

添加了模态框，但是一直偏网页上部，想让其居中，搜了下很多方法都没成功，大概因为我用的bootstrap是v3.3.0，那些方法都有些陈旧了吧。

最后在SOF上找到了一个提问，方法如下：

    <style type="text/css">
    .modal {
      text-align: center;
    }
    .modal:before {
      display: inline-block;
      vertical-align: middle;
      content: " ";
      height: 100%;
    }
    .modal-dialog {
      display: inline-block;
      text-align: left;
      vertical-align: middle;
    }
    </style>

添加css样式即可。

ps：[Link](http://stackoverflow.com/questions/18422223/bootstrap-3-modal-vertical-position-center)
