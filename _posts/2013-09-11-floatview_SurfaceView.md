---
title: '悬浮窗添加SurfaceView'
layout: post
tags:
    - tech
    - android
    - java
---
Android桌面悬浮窗效果实现，仿360手机卫士悬浮窗效果：
http://blog.csdn.net/guolin_blog/article/details/8689140

Android 浮动窗口进阶——画中画，浮动视频（附Demo）：
http://www.cnblogs.com/mythou/p/3250302.html

Android桌面悬浮窗：
http://blog.csdn.net/shinay/article/details/7783276

[应用代码] 后台无预览拍摄：
http://www.eoeandroid.com/forum.php?mod=viewthread&tid=278012

android 悬浮窗使用surfaceview的问题：
http://blog.csdn.net/fly06102559/article/details/6996692

android悬浮窗口的实现：
http://blog.csdn.net/stevenhu_223/article/details/8504058

```java
package com.spyvideo;

import android.annotation.TargetApi;
import android.content.Context;
import android.view.SurfaceHolder;
import android.view.SurfaceView;
import android.view.WindowManager;

public class FloatSurface {
    private static WindowManager wm;
    private static WindowManager.LayoutParams params;
    private CameraPreview cameraPreview;
    private Context context;
    private SetCamera setCamera;
    
    //public static String ip = "192.168.0.58";

    public FloatSurface(Context context) {
        super();
        this.context = context;
        setCamera=new SetCamera();
    }

    public void floatView() {
        wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
        params = new WindowManager.LayoutParams();
        System.out.println("进入悬浮窗!!!!!!!!");
        /*
         * 如果设置为params.type = WindowManager.LayoutParams.TYPE_PHONE; 那么优先级会降低一些,
         * 即拉下通知栏不可见
         */
        params.type = WindowManager.LayoutParams.TYPE_SYSTEM_ALERT;
        //params.format = PixelFormat.RGBA_8888; // 设置图片格式，效果为背景透明

        /*
         * 下面的flags属性的效果形同“锁定”。 悬浮窗不可触摸，不接受任何事件,同时不影响后面的事件响应。
         */
        params.flags = WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL
                | WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE
                | WindowManager.LayoutParams.FLAG_NOT_TOUCHABLE;
        // params.flags = WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL
        // | WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE;

        // 设置悬浮窗的长得宽
        params.width = 100;
        params.height = 100;
        cameraPreview = new CameraPreview(context);
        wm.addView(cameraPreview, params);
    }

    // 预览界面CameraPreview
    @TargetApi(9)
    class CameraPreview extends SurfaceView implements SurfaceHolder.Callback {

        SurfaceHolder surfaceHolder;

        public CameraPreview(Context context) {
            super(context);
            System.out.println("进入CameraPreview!!!!!!!!");
            surfaceHolder = getHolder();
            surfaceHolder.addCallback(this);
            // mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS); // 4.0+
            // auto
        }

        public void surfaceCreated(SurfaceHolder holder) {
            try {
                setCamera.setup(holder, SpyActivity.ip);
                System.out.println("setCamera is setuped");
            } catch (Exception e) {
            }
            setCamera.start();
        }

        public void surfaceDestroyed(SurfaceHolder holder) {
            setCamera.stop();
        }

        public void surfaceChanged(SurfaceHolder holder, int format, int width,
                int height) {
            
        }
    }
}

```