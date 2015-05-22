---
title: 'CatchLog 添加分类搜索 实现弹出菜单式操作'
layout: post
tags:
    - tech
    - java
    - android
---

这几天被搞的头都大了，就在刚刚无意中百度发现了到底是哪里出现的错误。

这个把我搞的快崩溃了的问题就是：当我进行分类搜索之后，再进行关键字搜索或者全局搜索发现会出现分类搜索的信息，也就是冗余的信息。而我在MainActivity明明就调用stopService终止了该后台服务了，也验证了service是调用了onDestroy（）的。但是就是会出现无关的冗余信息。

问题出在这里：service虽然停止了，但是service里的thread没有终止。也就是服务终止了，但是新建的线程没有终止！

之后调用thread.interrupt()。就发现问题得到解决了。真想高呼一下啊，可惜办公室。。╮(￣▽￣")╭ 折磨我快三天的问题就解决了。虽然是个小程序的小问题，解决了还是很高兴的。

添加分类搜索功能：利用菜单弹出一个radiobox让用户选择想要的分类。

    private void sendSortType(String tag) { // 发送log类别给service
            bundle = new Bundle();
            bundle.putString("tag", tag);
            sortIntent = new Intent();
            sortIntent.putExtras(bundle);
            sortIntent.setClass(MainActivity.this, SortLogService.class);
            startService(sortIntent);
        }

        private void startSortLogService() { // 分类搜索log

            CharSequence[] sort = { "Verbose", "Debug", "Info", "Warning", "Error",
                    "Fatal" };
            AlertDialog.Builder alertDialog = new AlertDialog.Builder(
                    MainActivity.this);
            alertDialog.setTitle("请选择").setSingleChoiceItems(sort, -1, // Radio选择
                    new DialogInterface.OnClickListener() { // 点击事件

                        @Override
                        public void onClick(DialogInterface dialog, int which) {
                            // TODO Auto-generated method stub
                            String tag = null;
                            // int logSortTag=0;
                            System.out.println(which);
                            switch (which) {
                            case 0:
                                tag = "*:V";
                                sendSortType(tag);
                                break;
                            case 1:
                                tag = "*:D";
                                sendSortType(tag);
                                break;
                            case 2:
                                tag = "*:I";
                                sendSortType(tag);
                                break;
                            case 3:
                                tag = "*:W";
                                sendSortType(tag);
                                break;
                            case 4:
                                tag = "*:E";
                                sendSortType(tag);
                                break;
                            case 5:
                                tag = "*:F";
                                sendSortType(tag);
                                break;
                            }
                            registerLogBroadcastReceiver(); // 注册广播接收器
                            dialog.dismiss(); // 点选后对话框消失
                            startBtn.setEnabled(false);
                            stopBtn.setEnabled(true);
                            keyWordBtn.setEnabled(false);
                            clearBtn.setEnabled(false);
                            logTag = sortLog; // 设置logTag标识为分类搜索
                            TAG=3;
                        }
                    }).setNegativeButton("取消", null).show();
            //isServiceRunning();
        }

弹出菜单：

        /**
         * 弹出菜单
         */
        @Override
        public boolean onCreateOptionsMenu(Menu menu) {
            // Inflate the menu; this adds items to the action bar if it is present.
            getMenuInflater().inflate(R.layout.menu_items, menu); // 获取菜单样式
            return true;
        }

        @Override
        public boolean onOptionsItemSelected(MenuItem item) { // 菜单选项
            // TODO Auto-generated method stub
            switch (item.getItemId()) {
            case R.id.menu_item_1: // 开始
                startSearchAllLogService();
                break;
            case R.id.menu_item_2: // 关键字搜索log
                startSearchKeyWordLogService();
                break;
            case R.id.menu_item_3: // 分类搜索
                startSortLogService();
                break;
            case R.id.menu_item_4:
                registerLogBroadcastReceiver();
                break;
            case R.id.menu_item_stop: // 停止
                stopLogService();
                break;
            }
            return true;
        }