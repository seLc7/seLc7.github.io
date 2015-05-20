---
title: 'Ubuntu 13.04下配置Android源代码'
layout: post
tags:
    - tech
---

一点小经验。
下载安装Ubuntu13.04后，启动终端。

####1.Git

android源代码是采用git管理的，所以，先安装个git：

`sudo apt-get install git-core gnupg`

####2.其他的一些依赖包

由于后面需要用到解压的操作，不妨先下好这些依赖包。其中libsdl-dev可能会提供更新，我看见的版本是1.2.另外就是libwxgtk2.8-dev这个也会提供更新，总之，就是要是有提示更新的就写上最新的版本好即可。

`sudo apt-get install flex bison gperf libsdl-dev libesd0-dev libwxgtk2.8-dev build-essential zip curl`

####3.Java SDK

之前在网上看了不少直接在终端里利用命令配置的描述，但是我没试验成功。两方面吧，大都还是写着sun-java6-jre等等，可是sun公司已经被oracle收购了，如果直接更新的花，大概是oracl-java7-jre这个样子，因为oracle好像在主线上只提供java7的样子（没有详细的做），但是android开发还是利用的java6来编写的，所以，我们可以到oracl官网上查找旧的java jdk。有可能需要注册一下oracle的用户，然后同意一些协议后，就可以选择下载了，这里分了两种包，一种是32位的jdk-6u\*\*-linux-i586.bin，一种是64位的jdk-6u\*\*-linux-x64.bin注意看好自己的系统版本，选择合适的包下载。

下载好以后，一般会在主文件夹的下载里，剪切出来，在主文件里新建一个文件夹叫JDK（随意）什么的，粘帖进去即可。

在终端开始操作

`cd /home/user/JDK`    其中user是你的用户名，JDK就是新建的文件夹名称。

`chmod u+x jdk-6u**-linux-i586` 更改权限，后面的jdk是你下载的jdk包的名字。或者这么写`chmod 701 jdk-6u**-linux-i586`

`sudo ./jdk-6u**-linux-i586.bin`   解压缩，会提示yes或者no，写yes就可，或者看到more这个词就一直回车，其实就是让你看协议。
解压完之后，会在JDK文件夹里看到一个类似jdk1.6.0_45这个名字的文件夹。

现在需要配置一下java的环境变量。

`sudo gedit /etc/profile`  

在proifle文件后添加如下代码：

    #set java environment

    JAVA_HOME=/home/user/JDK/jdk1.6.0_14                    

    export JRE_HOME=/home/user/JDK/jdk1.6.0_14/jre

    export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH

    export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
    

其中user还是你的用户名，jdk1.6.0_14就是刚刚解压的出来的jdk文件夹。
添加完成后，保存，重启一下系统，在终端输入 java -version 应该就可以看到java的版本信息了。

####4.下载Android源代码
goolge提供了一个repo工具，实际上是个脚本文件，里面封装了下载Android源代码所需要的git命令。先下载repo

`wget https://dl-ssl.google.com/dl/googlesource/git-repo/repo`

`chmod a+x repo`

`sudo mv repo /bin/`

安装好repo后，新建个空文件夹，在里面执行repo命令就可言下载android源代码了。

    mkdir Android
    cd Android
    repo init -u https://android.googlesource.com/platform/manifest -b android-2.3.1_r1

这里最后一行的-b android-2.3.1\_r1是想要下载的android源代码的版本号，可以不写，那样就直接下载主线上最新的源代码，但是最新的是开发版，总会有些小问题，所不妨下比较成熟的版本，上面给出的是2.3.1,改成android-4.0.1\_r1就是4.0.1版本的了。具体需要什么可以查询后再下。

之后执行

`repo sync`

就开始同步android源代码啦。
有时可能看到卡死的现象，就是数据也不更新，整个都是静态不动的，可以按ctrl+z来暂停，然后再输入一遍repo sync就可以继续同步了。直到显示done这个关键字就表示下载完成了。

####5.Android源代码的编译

64位机下可以直接在文件下编译。

`~/Android$ make`

32位机下就需要该一些相关的文件了。

1. 打开build/core/main.mk文件，找到如下的内容：
 
    ifeq ($(BUILD_OS),linux)
     
     build_arch := $(shell uname -m)
     
     #Change the following line for building on a 32-bit system.
     
     #ifneq (64,$(findstring 64,$(build_arch)))
     
     $(warning ************************************************************)
     
     $(warning You are attempting to build on a 32-bit system.)
     
     $(warning Only 64-bit build environments are supported beyond froyo/2.2.)
    
    将第三行改为
     ifneq (i686,$(findstring i686,$(build_arch)))

2. 找到下列文件：
     
     /external/clearsilver/cgi/Android.mk
    
     /external/clearsilver/cs/Android.mk
    
     /external/clearsilver/java-jni/Android.mk
    
     /external/clearsilver/util/Android.mk
    
     修改LOCAL_CFLAGS和LOCAL_LDFLAGS变量：
    
     # This forces a 64-bit build for Java6
    
     # Change the following two lines for building on a 32-bit system.
     
     # LOCAL_CFLAGS += -m64
    
     # LOCAL_LDFLAGS += -m64
    
    将后两行修改为
     
     LOCAL_CFLAGS += -m32
     
     LOCAL_LDFLAGS += -m32

之后make即可。

有可能会出现各种各样的错误，这时候就需要baidu&google出来帮忙了。

最后编译完成后，可以将其打包成sdk：
`~/Android$ make sdk`

打包后，就可以在IDE环境中开发Android应用程序了。

以上就是我的一点小经验。

Ps：第一次在ubuntu下码字。。。


###编译时的一些错误：

* 错误一：
    
    编译时出现如下的错误：
    
    `make:***[out/host/linux-x86/obj/EXECUTABLES/obbtool_intermediates/Main.o]Error/`
    
    是由于gcc版本太高
    
    安装gcc 4.4 
    
    `sudo apt-get install gcc-4.4`
    
    `sudo apt-get install g++-4.4`
   
    装完后，在/usr/bin目录下
    
    `$ ls -l gcc* `           可以看到gcc链接到哪个版本。
    
    `sudo mv gcc gcc.bak`
    
    `sudo ln -s gcc-4.4 gcc`
    
    同理，`ls -l g++*`可以看到g++是哪个版本
    
    `sudo mv g++ g++.bak`
    
    `sudo ln -s g++-4.4 g++`
    
    最后查看一下版本号， `gcc -v`     `g++ -v`

* 错误二：（未解决）
    
    `/bin/bash: prebuilt/linux-x86/toolchain/arm-eabi-4.4.3/bin/arm-eabi-gcc: `

    没有那个文件或目录
    
    使用`apt-get upgrade glibc`，升级glibc
    
* 错误三：
    
    编译成功后，运行emulator时，提示
    >未找到 'emulator' 命令，您要输入的是否是：
    
    >命令 'qemulator' 来自于包 'qemulator' (universe)
    
    >emulator：未找到命令
    
    重新运行以下命令：
    `source build/envsetup.sh`
    
    `lunch full-eng`
    
    `emulator`