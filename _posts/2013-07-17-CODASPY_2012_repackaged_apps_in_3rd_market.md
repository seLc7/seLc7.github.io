---
title: 'CODASPY 2012_Detecting Repackaged Smartphone Applications in Third-Party Android Marketplaces'
layout: post
tags:
    - paper
    - android
---

阅读笔记：

介绍了一些对市场上应用软件情况的统计信息。

1. DroidMoss   fuzzy hashing technique
    
    应用模糊散列技术
    
    将第三方市场的app与官方市场的比较。从大量的apps中检测、提取apps的特征值，并生成应用程序特定的指纹。
    
    finger generation

    计算每个程序对（真与仿真）的相似距离，超出一定的阈值，就认为被重打包过。
    
    3 key steps:

    1>应用程序包含的指令和作者信息，来确认app
                        
    2>给每个app一个指纹标识
    
    3>识别程序源，从官方和第三方。

2. three categories
    
    1）同样存在于 official android market
         
    reach more users
    
    2）只在第三方市场有的
    
    targeting specific customers （regions，countries，languages....)
    
    3）主要的关注点
    
    从官方市场上重新打包再在第三方市场投放的

3. design
   
    Focus on Java code without native code.(native code只有一小部分apps包含,harder for repackager to modify)

4. 其他
    
    1）比对时不一定官方市场里就没有重打包过的。有可能第三方市场是原型儿官方市场是重打包的。
    
    2）不包含付费软件
    
    3）使用共享库或广告sdk的应用程序重打包的检测