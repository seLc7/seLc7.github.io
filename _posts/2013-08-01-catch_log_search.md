---
title: 'CatchLog 关键字搜索（往service传值，service取值）'
layout: post
tags:
    - tech
    - android
    - java
---

记录一点：某项工作做到一定程度之后，如果要修改一定要备份一下，无论是个工程还是个txt！

首先学习到的是，通过intent发送值给service。

    public void onClick(DialogInterface dialog, int whichButton) {

            EditText keyWordText = (EditText) view.findViewById(R.id.input_keyword_edittext);
            String keyWord=keyWordText.getText().toString();                          //获取输入的关键字
            Toast.makeText(MainActivity.this, "开始关键字"+keyWord+"搜索", Toast.LENGTH_SHORT).show();
                            
            bundle=new Bundle();
            bundle.putString("keyWord", keyWord);
            keyWordIntent=new Intent();
            keyWordIntent.putExtras(bundle);
            keyWordIntent.setClass(MainActivity.this, SearchKeyWordService.class);
            startService(keyWordIntent);
            registerLogBroadcastReceiver();
            startBtn.setEnabled(false);
            stopBtn.setEnabled(true);
            logTag=keyWordLog;   //设置标识为关键字搜索log信息
        } 

但是从service里读取值的时候并不是像网上说的intent.getIntent().getExtras()。因为在onCreate里甚至没有getIntent()这个函数可以调用。可以采用onStart()（2.0之前？）函数，或者onStartCommand()函数，参考如下。

    public int onStartCommand(Intent intent, int flags, int startId) {
            // TODO Auto-generated method stub
            Bundle getBundle = intent.getExtras();                   //获取关键字
            keyWord=getBundle.getString("keyWord");                         
            return super.onStartCommand(intent, flags, startId);
        }
