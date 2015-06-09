---
title: 'Activity 变成对话框，然后再隐藏？'
layout: post
tags:
    - tech
    - java
    - android
---
由于继续需要做一些无感操控的工作，之前也记录了下利用悬浮窗的方法。今天突然发现原来activity可以直接嗯嗯嗯啊。

首先我在AndroidManifest里给activity添加了如下一行：

`android:theme="@android:style/Theme.Dialog"`

ok，我发现activity变成对话框的弹出模式了。但是背景也就是桌面是变暗的，我希望能恢复正常的亮度，也就是透明的效果，于是继续搜索。
发现：

`android:theme="@android:style/Theme.Translucent" `

这个应该可以让activity变成透明的，但是不能声明两次Theme啊，于是继续找办法，发现，哦，我可以声明style啊。
在style 的xml 里 设置如下：

```xml
<resources>  
    <style name="dialog" parent="@android:style/Theme.Dialog">  
        <item name="android:backgroundDimEnabled">false</item><!--activity不变暗-->  
    </style>  
</resources>  
```

然后将manifest里改成：`android:theme="@style/dialog"`

嗯，继续，我希望没有标题，那么添加`<item name="android:windowNoTitle">true</item>`

此时我以为就达到目的了，于是我修改了layout文件的布局大小，宽高都修改成1dp，但是我发现程序运行是屏幕上有个框。。。原来是activity的黑边，我要消掉他。
于是`<item name="android:windowFrame">@null</item>`

经过不断的修改之后，最后的style.xml就变成了：

```xml
<resources>
    <style name="dialog" parent="@android:style/Theme.Dialog">  
        <item name="android:windowFrame">@null</item><!-- activity无黑边 -->  
        <item name="android:windowIsFloating">true</item><!-- activity可滑动 -->  
        <item name="android:windowIsTranslucent">false</item><!-- activity透明 -->  
        <item name="android:windowNoTitle">true</item><!-- activity无标题 -->  
        <item name="android:background">@android:color/black</item>  
        <item name="android:windowBackground">@null</item>  
        <item name="android:backgroundDimEnabled">false</item><!-- activity不变暗 -->
    </style>  
</resources>
```

现在看起来好多了，但是又发现了一个问题，就是点击activity之外的地方，activity自己就退出了，直接回到了桌面。。。
那么onCreate()方法里加上这句 setFinishOnTouchOutside(false);或者在style里添加:

`<item name="android:windowCloseOnTouchOutside">false</item><!-- activity点击空白部分不消失 -->`

（以上不消失activity的方法需要api11以上，版本低的话需要再进行一些函数操作，可参考：http://blog.csdn.net/voiceofnet/article/details/9061309）