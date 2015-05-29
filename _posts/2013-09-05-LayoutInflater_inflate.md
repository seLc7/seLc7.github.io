---
title: 'LayoutInflater的inflate函数用法详解（也setContenView的区别）'
layout: post
tags:
    - tech
    - android
    - java
---

转载于：http://www.open-open.com/lib/view/open1328837587484.html

LayoutInflater作用是将layout的xml布局文件实例化为View类对象。

获取LayoutInflater的方法有如下三种:

```java

LayoutInflater inflater=(LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
View layout = inflater.inflate(R.layout.main, null);
 
LayoutInflater inflater = LayoutInflater.from(context); // 该方法实质就是第一种方法，可参考源代码
View layout = inflater.inflate(R.layout.main, null);
 
LayoutInflater inflater = getLayoutInflater(); // 在Activity中可以使用，实际上是View子类下window的一个函数
View layout = inflater.inflate(R.layout.main, null);

```
一直有点纠结setContentView和inflate的区别找了一些资料。写了个小程序看了下：

```java
public class MyInflate extends Activity{
    private TextView tv;
    public void OnCreate(Bundle savedInstanceState){
        super.onCreate(savedInstanceState);
        //setContentView(R.layout.main);
        //tv = (TextView) findViewById(R.id.tv); 
        LayoutInflater inflate = LayoutInflater.from(this);
        View view = inflate.inflate(R.layout.main,null);
        setContentView(view);
    }
}
```

上述注释掉的代码和没有注释掉的代码两种情况是相同的。

**区别：**

setContentView()一旦调用, layout就会立刻显示UI；而inflate只会把Layout形成一个以view类实现成的对象，有需要时再用setContentView(view)显示出来。一般在activity中通过setContentView()将界面显示出来，但是如果在非activity中如何对控件布局设置操作了，这就需要LayoutInflater动态加载。

public View inflate(int Resourece,ViewGroup root)
作用：填充一个新的视图层次结构从指定的XML资源文件中

reSource：View的layout的ID

root： 生成的层次结构的根视图

return 填充的层次结构的根视图。如果参数root提供了，那么root就是根视图；否则填充的XML文件的根就是根视图。

其余几个重载的inflate函数类似。