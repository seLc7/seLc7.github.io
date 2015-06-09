---
title: 'Android: Uid & Pid, etc.'
layout: post
tags:
    - tech
    - java
    - android
---
1. pid：一个pid对应一个进程，系统会自动分配给新打开的进程一个独一无二的pid。进程终止后，pid会被系统回收。新产生的pid，一般都比之前所有的pid的进程号大，也就是说回收了的进程号不一定会被使用，而是分配新的pid给新进程。

2. uid：每一个不同的程序都有一个uid（而不是常说的那种用户id），一个应用里可以有多个pid，uid是在app安装是被系统赋予的，是不变的。除非卸载了重装，又会赋予一个uid。关于数据共享：默认情况下，Android会给每个程序分配一个普通级别互不相同的 Uid，如果用互相调用，只能是Uid相同才行，这就使得共享数据具有了一定安全性，每个软件之间是不能随意获得数据的。而同一个application 只有一个Uid，所以application下的Activity之间不存在访问权限的问题。

3. tid：thread id，线程id。

4. 3之后android增添多用户功能，所以又有了另一种id，忘记叫什么了，有人知道请告之一声。

references：

http://blog.csdn.net/thl789/article/details/7602722
http://bbs.csdn.net/topics/380225593
http://www.cnblogs.com/perseus/articles/2354173.html