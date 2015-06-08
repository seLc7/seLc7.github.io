---
title: 'SharedPreferences 学习'
layout: post
tags:
    - tech
    - java
    - android
---

一般Android软件都提供几个配置选项给用户选择，用到的是SharedPreferences类持有配置信息，一般除了取得配置信息，还可以使用SharedPreferences.Editor修改配置信息，它的本质是基于XML文件存储key-value键值对数据，通常用来存储一些简单的配置信息。其存储位置在`/data/data/<包名>/shared_prefs`目录下。SharedPreferences对象本身只能获取数据而不支持存储和修改，存储修改是通过Editor对象实现。

这对Android程序恢复到上次退出的状态十分有用。之前有想保存到数据库中去的冲动，还好搜到。

2个activity 之间的数据传递除了可以他通过intent来传递,还可以使用SharedPreferences来共享数据的方式。

SharedPreferences 用法很简单，如果你想要编辑SharedPreferences中的内容就需要用到editor对象。
举例如下：

A中：

```java
Editor sharedata = getSharedPreferences("data", 0).edit();  
sharedata.putString("item","hello getSharedPreferences");  
sharedata.commit();  
```

B中：

```java
SharedPreferences sharedata = getSharedPreferences("data", 0);  
String data = sharedata.getString("item", null);  
Log.v("cola","data="+data); 
```

通过以上例子，我们看到，通过
`public abstract SharedPreferences getSharedPreferences (String name, int mode)`
方法得到一个sharedpreferences对象，参数name是preference文件的名字，mode则是方式，默认为0。

然后是通过SharedPreferences的editor（）方法得到editor对象，之后可以进行相应的编辑操作。

以上例子中用到的editor对象方法如下
`public abstract SharedPreferences.Editor putString (String key,String value)`
通过执行commit（）或是apply（）方法，将会应用更改。

下面就来说说apply和commit把。相同点：

1. 二者都是提交preference修改数据
2. 二者都是原子过程。

区别：

1. apply没有返回值而commit返回boolean表明修改是否提交成功
2. apply是将修改数据原子提交到内存，而后异步真正提交到硬件磁盘；而commit是同步的提交到硬件磁盘，因此，在多个并发的提交commit的时候，他们会等待正在处理的commit保存到磁盘后在操作，从而降低了效率。而apply只是原子的提交到内容，后面有调用apply的函数的将会直接覆盖前面的内存数据，这样从一定程度上提高了很多效率。
3. apply方法不会提示任何失败的提示。

综合上述，由于在一个进程中，sharedPreference是单实例，一般不会出现并发冲突，如果对提交的结果不关心的话，建议使用apply，当然需要确保提交成功且有后续操作的话，还是需要用commit的。

commit介绍：public abstract boolean commit ()

修改你的preferences，从Editor到SharePreferences。它执行所请求的修改，替代SharedPreferences中的任何数据
当2个editor同时修改preferences ，最后一个commit成功。

如果不关注返回值或在程序的main线程使用时，推荐使用apply()。

apply介绍：public abstract void apply ()

区别：

commit将同步的将数据写到preferences；apply立即更改内存中的SharedPreferences，但是开始异步提交到磁盘中。保存失败你也不会得到任何提示信息，

如果在这个sharedPreferences有另外一个editor执行一个定期的commit，此时一个apply依旧未完成。commit将被阻塞，直到所有异步操作完成，以及自己的commit。
由于SharedPreferences在进程中是单实例的。在忽悠返回值的前提下，取代任何实例的commit或apply都是安全的。

ps：

```java
private SharedPreferences settings;  
SharedPreferences.Editor editor = settings.edit();  
editor.putString("ip", ip);  
editor.commit();  
```

references：

http://blog.csdn.net/renero/article/details/6635728

http://menuz.iteye.com/blog/1235308

http://tanqi0508.blog.163.com/blog/static/1883557772012111104326404/

http://www.cnblogs.com/wisekingokok/archive/2011/09/16/2177833.html