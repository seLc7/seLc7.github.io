---
title: 'CCS13_AppIntent: Analyzing Sensitive Data Transmission in Android for Privacy Leakage Detection'
layout: post
tags:
    - paper
    - android
---

主要是开发了一个框架：_AppIntent_，研究的核心问题就是：如何来判断一次信息的传输是否是基于用户意愿的。因为很多时候窃密动作的发生是用户不知情的，显然也不是用户的意愿产生的信息传输。

而且提出了一种观点，认为app自己进行的敏感信息传输并不一定就真的是隐私泄露，有可能是良型app为了和云端同步而自动上传的更新数据，所以他们认为一个好的indicator应该是能够分辨出来这个传输是否是用户意愿的。

符号执行（`symbolic execution`）是一个有效的技术手段来提取触发程序特殊行为的输入动作，比如一次敏感信息的传输。现有的一些符号执行工作都是聚焦于无交互的程序，而Android app却有多种交互或触发方式，所以他们为了解决这个难题提出了事件空间约束引导符号执行（`event-space constraint guided symbolic execution`）。首先静态分析目标app来确认会引发敏感信息传输的可能执行路径（例如发送SMS）。然后利用这些可能的路径作为基础来生成事件空间约束，之后就只考虑满足事件空间约束的路径来作为引导符号执行的入口。

为了评价AppIntent的有效性，使用了真实的app程序，其中包括750个恶意app和1000个Google Play上的免费app。实验结果表明，252个app有敏感信息传输，其中224个app包含了用户不希望的传输，28个只包含了用户意愿的数据传输。

AppIntent不是一个自动检测非意愿信息传输的方法（好像也不可能实现），它只是提供给分析人员敏感信息传输时事件序列的UI操作过程，来促进对于敏感信息传输是否是用户意愿的辨别。

整体框架由两部分组成：

* Event-space Constraint Guided Symbolic Execution. (static taint analysis; critical events; essential events)
* Dynamic Program Analysis Platform. (InstrumentationTestRunner)

