---
title: 'TaintDroid: An Information-Flow Tracking System for Realtime Privacy Monitoring on Smartphones'
layout: post
tags:
    - paper
    - android
---

`一些笔记，包括一些网上搜罗到的要点（感觉？）后面的文章内容，基本自翻。`

TaitnDroid的思想就是将所有的隐私数据变成污染源，在程序运行的过程中若对污染源进行截取、拼装、加密、传递等操作，那么新生成的数据也会被污染。

* 污点的存储
* 解释代码污点传播[Dalvik解释器]
* 本地代码污点传播[Native code]
* IPC污点传播
* 二级存储污染。

污染的三大途径：**赋值**，**参数传递**，**返回值**。

Dalvik之上是变量级的污染，Native是调用级的污染，存储是文件级的污染，IPC还有消息级别的污染。

---------------------------

__文章内容__：

污点传播粒度（granularities of taint propagation)

+ variable-level  变量级
+ method-level  方法级
+ message-level  消息级
+ file-level 文件级

tracking： 

+ ①only for data and not code，by tracking variabls

    跟踪变量时，只针对数据而不是代码。
+ ②use message-level tracking between applications

    在应用程序直接利用消息级的跟踪
+ ③use method-level tracking for system-provide native libraries

    对于系统提供的本地库，利用方法级的跟踪
+ ④use file-level tracking to ensure persistent information conservatively retains its taint markings
    
    利用文件级的跟踪确保持久的信息适当的保留了污点标记。

#####4.1  污点存储标记
    
Dalvik有五种类型的变量要求存储：局部方法变量（method local variables），方法的参数（method arguments），类的静态变量（class static fields），类实例的字段值（class instance fields）和数组（arrays）。在所有的情况下，对于每种变量存储一个32位的位向量进行污染标记编码，允许32种不同的污点标记方式。

variables    interleaved（交错的）

![](/files/2013/20130719_01.jpg)

Dalvik用两个在内部栈分开的32位寄存器存储64位的变量。当字节码将这些分开的寄存器中的值解释为一个单独的64位值时，实际上，解释器还是将这些变量管理成为两个分开的值。
所以修改了栈的存储方式，并将两个分开的32位寄存器恢复成64位的值。

对于数组，TaintDroid只存储一个污点标记给每个数组来缩小存储开销。但是有时会有错误发生。

**e.g.** 

1. 参数u  `untainted（未污染） →A[0]`         <未污染的u存入数组A>
2. 参数t   `taintde（污染了）→A[1]`      <污染了的t也存入了数组A，此时数组A被标记为污染>
3. 参数v   `→A[0]`                                     <v存入数组A[0]，v将被污染，而u还是未污染的>

幸运的是，java是面向对象的，而对象的调用是很少被标记污点的，因此代码实践将导致较少的误报。( Fortunatly, Java frequently uses objects and object references are infrequently tained, therefore this coding practice leads to less false positives.)

#####4.2 Interpreted Code Taint Propagation 解释代码污点传播

污点传播逻辑：Taint Propagation Logic

![](/files/2013/20130719_02.jpg)

+ L：对于特定系统的污点标记集合    the universe of taint markings for a particular system
+ t：污点标记集合，从属于L  taint tag t is a set of taint markings
+ Vx：局部参数变量  local argument variables
+ fx：类的域变量，声明一个类字段变量用x来索引（类为x？）  Class field variables， toindicate a field varibles with class index x
+ Vy(fx)：实例域需要一个实例对象，Vy就是实例对象的引用  instance fields require an instance object.Vy is the instance object reference.
+ fx：静态域值，是S(fx)的缩写，S()是静态取值范围  static fields shorthand for S(fx).S() is the static scope.
+ Vx[·]：数组
+ t（·）：污点标记函数。t（v）返回参数v的污点标记t  taint map function. t(v) returns the taint tag t for variable v.
+ t(v1) ← t(v2)：从v2复制污点标记给v1  copies the taint tag from v2 to v1

R and E    return and exception

A.B.C constants in the byte-code  字节码常量。

![](/files/2013/20130719_04.jpg)

![](/files/2013/20130719_03.jpg)

考虑这种情况，tVal是一个整数，值为1，并标记了污点标记TAG。当intProxy（）被传入tVal（实参？）时，TAG就被传播给了val（形参？）。当intProxy（）返回val时，它调用Integer.valueOf（）来获取一个整形实例来对应val，Integer.valueOf（）返回一个值为1的静态整型对象的引用。该对象的值域（of the Integer class）有污染标记∅。但是因为aget-op传播操作规则包含了索引寄存器的污点标记，所以这个对象引用有一个污点标记TAG。因此，只有通过在从整型读取值域时包括对象引用的污点标记（也就是iget-op传播规则），将修正被分配出的污点标记TAG。

>Consider the case where tVal is an int with value 1 and taint tag TAG. When intProxy() is passed tVal, TAG is propagated to val. When intProxy() returns val, it calls Integer.valueOf() to obtain an Integer instance corresponding to the scalar variable val. In this case, Integer. valueOf() returns a reference to the static Integer object with value 1. The value field (of the Integer class) in the object has taint tag of ;; however, since the aget-op propagation rule includes the taint of the index register, the object reference has a taint tag of TAG. Therefore,only by including the object reference taint tag when the value field is read from the Integer (i.e., the iget-op propagation rule), will the correct taint tag of TAG be assigned to out.

#####4.3 Native code Taint Propagation

unmonitored TaintDroid 不检测本地库代码

two necessary postconditons for  accurate taint tracking in the Java-like environment:

* 所有存储的外部变量要根据数据流规则来分配污点标记。
* 标记污点的返回值也要根据数据流规则。

Internal VM Methods：直接被解释码调用，传递一个指针给一个由32位寄存器参数组成的数组和一个传递给返回值的指针。

JNI Methods：通过JNI调用桥调用JNI方法。调用桥解析Java参数，并使用函数的描述符字符窜来分配一个返回值，include an additional propagation heuristic patch.

#####4.4 IPC Taint Propagation

message-level

通过变量级来实现消息级的污点传播，因为在变量级的系统，狡猾的消息接收者可以通过不同的方式解包变量来获得值，从而戏耍监控者并且不受污染传播标记。

leads to false positives

未来的实现将考虑word级的污染标记以及额外的一致性检测来保证对于未打包的变量正确的污染传播。

>Future implementations will consider word-level taint tags along with additional consistency checks to ensure accurate propagation for unpacked variables.

#####4.5 Second Storage Taint Propagation

二级存储

Taint tags may be lost when data is written to a file.
one taint tag per file.

The tiant tag is updated on file write and propagated to data on file read.
TaintDroid stores file taint tags in the file syestem's enxtended  attributes.

实现了扩展属性支持安卓系统的主机文件系统（YAFFS2）并将SD卡格式化为ext2文件系统。
或者可以以更细的粒度牺牲更多的内存和性能开销来跟踪污点标记。

#####4.6 Taint Interface Library

2 functions:
+ ①add taint markings  to variables.
+ ②retrieve………………from…………（检测。。。

Low-bandwidth Sensors：e.g. location accelerometer
changes frequently

simultaneously used by multiple applications

High-bandwidth Sensors: mircophone and camera

a large amount of data

only used by one application

Information Database: address books       SMS messages.
stored in filebased database.

Device Identifiers: phone numbers    IMSI   ICC-ID   IMEI
Network Taint Sink:network interface
