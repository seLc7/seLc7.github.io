---
title: '搜索框问题解决'
layout: post
tags:
    - tech
    - bootstrap
---

终于解决了搜索框无法提交提交关键字搜索的问题。

    <input type="text" class="form-control" placeholder="Search..."
            id="search"  onkeydown="if(event.keyCode==13){info(this.id);return false;}">

重点在于就是回车键提交了表单。加入了`return false`才使得搜索成功。具体的原因还不是太明白。查询了下说是避免input中的输入项丢失。

猜想是由于回车后提交了表单然后input自动将value置为null，所以就获取不到值了？阻止了表单提交，则value值还是在的，所以就成功了？

参考了下百度搜索框和淘宝的，发现里面都写了`return false`。

搜了下csdn，有人是这么说的:

>在IE中发现一个奇怪的问题,在页面的一个input输入框时,回车会触发表单提交。
>后来发现这是浏览器的默认行为,在表单中只有一个input文本框时,回车会触发表单提交.
>[Link](http://bbs.csdn.net/topics/390710072?page=1)

