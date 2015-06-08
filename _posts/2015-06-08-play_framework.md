---
title: 'Play Framework'
layout: post
tags:
    - tech
    - java
---

Play框架的安装是利用activator自动化式的，根据官网教程按步骤来即可。

配置完activator的环境变量后，就可以建立项目了。

1. `activator new`
2. 选择模式，play-java，之后输入name，回车，项目建立
3. 如果需要使用eclipse IDE，则需要在项目project目录下的plugin.sbt中添加
    
    `addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "3.0.0")`

    之后输入命令`activator eclipse`。

我选择了使用sublime编辑器直接编辑。初次使用主要一个问题是如何调用css和script。

在__routes__里添加

`GET     /assets/*file             controllers.Assets.at(path="/public", file)`

把想要调用的js和css文件放入public文件夹下。

例如想要调用/public/css/bootstrap/bootstrap.css，则只需要在html中添加

`link href="@routes.Assets.at("css/bootstrap/bootstrap.css")" rel="stylesheet" />`

想要调用/public/js/jquery-latest.js,则添加

`<script src="@routes.Assets.at("js/jquery-latest.js")"></script>`


[参考文章](http://www.cnblogs.com/vamei/p/3722495.html)