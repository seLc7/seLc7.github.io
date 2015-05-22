---
title: 'CatchLog 添加alertdialog'
layout: post
tags:
    - tech
    - android
    - java
---

一些要点：

setContentView和inflate的区别：

setContentView()一旦调用, layout就会立刻显示UI；而inflate只会把Layout形成一个以view类实现成的对象，有需要时再用setContentView(view)显示出来。一般在activity中通过setContentView()将界面显示出来，但是如果在非activity中如何对控件布局设置操作了，这就需要LayoutInflater动态加载。

public View inflate(int Resourece,ViewGroup root)
作用：填充一个新的视图层次结构从指定的XML资源文件中

reSource：View的layout的ID

root： 生成的层次结构的根视图

return 填充的层次结构的根视图。如果参数root提供了，那么root就是根视图；否则填充的XML文件的根就是根视图。

其余几个重载的inflate函数类似。

详情：http://www.open-open.com/lib/view/open1328837587484.html

    keyWordBtn.setOnClickListener(new OnClickListener() {           //输入关键字  
                  
                @Override  
                public void onClick(View v) {  
                    // TODO Auto-generated method stub  
                    AlertDialog.Builder builder = new AlertDialog.Builder(MainActivity.this);  
                    LayoutInflater inflater = LayoutInflater.from(MainActivity.this);  
                    final View view = inflater.inflate(R.layout.dialog_search, null);  
                      
                    builder.setTitle("查找关键字Log信息");  
                    builder.setView(view);  
                    builder.setPositiveButton("搜索", new DialogInterface.OnClickListener() {  
                        public void onClick(DialogInterface dialog, int whichButton) {  
      
                            EditText keyWordText = (EditText) view.findViewById(R.id.input_keyword_edittext);  
                            String keyWord=keyWordText.getText().toString();  
                            Toast.makeText(MainActivity.this, keyWord, Toast.LENGTH_SHORT).show();  
                              
                        }  
                    });  
                      
                    builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {  
                        public void onClick(DialogInterface dialog, int whichButton) {  
                          
                        }  
                    });  
                    builder.create().show();  
                    serviceFlag=2;  
                }  
            });  


EditText控件获取输入内容，需要调用getText()

现阶段考虑的是另外启动一个service支持关键字查找。但是考虑到停止按钮的相关设置，所以放置了一个int 型的serviceFlag来标识。seviceFlag=1时进行全局扫描。=2时进行关键字扫描。后面考虑可以置换成String型的tag标识。