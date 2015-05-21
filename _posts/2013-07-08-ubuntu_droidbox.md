---
title: 'ubuntu13.04 下安装DroidBox'
layout: post
tags:
    - tech
---

[Droidbox](http://code.google.com/p/droidbox/)(http://code.google.com/p/droidbox/)

[Android SDK](http://developer.android.com/sdk/index.html)(http://developer.android.com/sdk/index.html)

由于droidbox是一个沙盒，需要在模拟器环境下运行，所以，我们需要几个环境配置。

1. ubuntu的下载配置。我采用的是13.04版本。官网下载就可。

2. 终端需要写入几个环境变量：

    `exportPATH=$PATH:/path/to/android-sdk/tools/`

    `export PATH=$PATH:/path/to/android-sdk/platform-tools/`

3. android sdk 下载android 

    sdk，ubuntu下解压该压缩包，之后在终端进入文件夹，输入命令：android即可启动AVD Manager，选择要下载更新的sdk包，由于droidbox开发环境适合2.1和2.3，所以选择2.3来下载。

    下载好之后，可以输入：`android list targets`

    会列出本机已经下载好的sdk类似于如下的形式：

    >Id：3 or  “android-10”
    
    >Name: Android2.3.3
    
    >Type: Platform
    
    >API level: 8
    
    >Revision: 3
    
    >Skin:……..
    
    >……

    之后我们就可以创建一个AVD了，形式为“android create avd –name <your_avd_name> --target<targetID>”，我们在终端输入如下的命令：

    `android  create avd  --name  android2.3.3 --target  android-10`

    可以使用emulator是试验启动：

    `emulator –avdandroid2.3.3`

4. 在终端输入命令下载：

    `wget http://droidbox.googlecode.com/files/DroidBox.tar.gz`

    下载完成后解压，在终端进入文件夹

5. 这个工具主要使用Python开发，使用的时候还需要安装一个Python模块pylab,只需要在终端输入：
    
    `sudo apt-get install python-matplotlib`

    安装pylab模块即可

6. 在droidbox文件夹里可以看到两个脚本startemu.sh 和droidbox.sh 。

    在终端进入文件夹后输入：

    `./startemu.shandroid2.3.3`(这个就是创建的avd的名字)

    之后可以看到avd启动。启动成功后，在终端输入：

    `./droidbox.sh./virus.apk`(这个就是你想要在沙盒里运行的apk文件名，当然要把这个apk放到droidbox文件下啦)

    之后droidbox就会运行，等到“collect xx sandbox logs”信息显示的数值长时间不发生变化了，可以按下Ctrl+C来停止droidbox。之后分析即可。
 
    如果安装过程遇到各种错误，百度，google是最好的帮助。

    安装python：`apt-get install python-simplejson`