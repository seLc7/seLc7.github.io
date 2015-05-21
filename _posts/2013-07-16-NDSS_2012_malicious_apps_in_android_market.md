---
title: 'NDSS 2012_Hey You Get off of My Market Detecting Malicious Apps in Official and Alternative Android'
layout: post
tags:
    - paper
    - android
---

一些笔记，名词基本都是自翻

DroidRanger：第一个系统的研究官方和非官方市场的软件。
                        
>最广泛的      

>使用两种模式

1. permission-based behavior footprinting scheme（基于权限的行为足迹匹配）

    从已知的安卓恶意程序家族中检测到新的样本。

2. heuristics-based filtering scheme（基于启发式的过滤模式）

    用于确定未知的恶意程序。

    设计上：

    KEY：accuracy，scalability，efficiency（准确性，可扩展性，有效性）

    ######2.1

    1）基于权限 permission-based filtering
    
    选择过滤权限时要从基本的基础库里选择，不然容易发生误判。
    
    e.g.  **ZSone** 
        
        *send SMS messages 为了隐藏消息，提供保险号码给恶意方，引起用户的财产损失，监控消息并移除账单提醒。*
        
        在Android下，需要SEND_SMS and RECEIVE_SME两个权限。
    
    2）行为匹配 behavior footprinting matching
    
    行为特征：
        
    i>manifest里注册的组件及其目的
        
    ii>特定组件的代码解析，提取API调用序列
        
    iii>代码结构布局
    
    e.g. **ZSone**
       
       ①监听。a receiver
       
       ②发送信息给“10621900”etc. 泄漏信息
       
       ③拦截短信。“10086”etc. 拦截账单提醒
    
    ######2.2

    1）基于启发式的过滤 heuristics-based filtering

    i>从远程未可信网站上动态下载Java二进制代码。
   
    `DexClassLoader  下载文件.apk .jar  广告库`
    
    `Plankton spyware`
    
    ii>从本地动态下载原代码（native code）
    
    DroidKungFu 在非默认路径里加载本地代码执行提取代码。
    
    2）动态执行的监测 dynamic excution monitoring
    
    i>对动态加载的jar和dex记录其API调用及参数
     `SendTextMessage`
    
    ii>对动态加载的native code 记录其发生的敏感系统调用
    `sys_mount`
    
    人工对这些日志进行分析。
3. 评估
    发现Plankton 下载jar文件前，会将一些权限通过远程服务器赋予app。

