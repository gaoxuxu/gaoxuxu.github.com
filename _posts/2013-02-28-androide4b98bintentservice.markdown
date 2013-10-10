---
author: gavin
comments: true
date: 2013-02-28 14:10:33
layout: post
slug: android%e4%b9%8bintentservice
title: Android之IntentService
wordpress_id: 56
categories:
- Android
- 源码分析
tags:
- Android
- HandlerThread
- IntentService
---


	IntentService是一种用于处理异步请求（表现为Intent）的服务基类。客户通过调用Context.startService(Intent)发送请求；根据需要启动服务，依次使用工作者线程处理每一个Intent，并且在工作完成后终止自身。






	这种“工作队列处理器”模式通常被用来从一个应用程序的主线程卸载任务。IntentService类的存在是为了简化这种模式，并且照顾了技术性的部分。要使用它，需要继承IntentService类并且实现onHandleIntent(Intent)方法。IntentService将会接收到这些Intent，运行一条工作者线程，并且在适当的时候停止服务。






	所有的请求都是在一个单一的工作者线程中处理的，尽管处理过程可能需要很长时间（不会阻塞应用程序的主线程），但是每次只能有一个请求会被处理。






	首先，看它提供的一个构造方法：






	  





    
    public IntentService(String name) {
        super();
        mName = name;
    }





	  







	参数name被用于命名即将用到的工作者线程，它的重要性仅用于调试。这里需要注意的一点是，Service的实例化是系统来完成的，并且系统是用无参的构造方法来实例化Service的。所以，你的子类必须是无参的，然后在无参构造方法里调用super("name")。






	再看onCreate方法：






	  





    
    @Override
    public void onCreate() {
        super.onCreate();
        HandlerThread thread = new HandlerThread("IntentService[" + mName + "]");
        thread.start();
    
        mServiceLooper = thread.getLooper();
        mServiceHandler = new ServiceHandler(mServiceLooper);
    }





	  







	其中的ServiceHandler是IntentService类的内部类：






	  





    
    private final class ServiceHandler extends Handler {
        public ServiceHandler(Looper looper) {
            super(looper);
        }
    
        @Override
        public void handleMessage(Message msg) {
            onHandleIntent((Intent)msg.obj);
            stopSelf(msg.arg1);
        }
    }





	  







	在onCreate方法里，创建并启动了一条工作线程。然后使用工作线程的Looper构造了一个Handler，这个Handler将循环处理请求。






	根据Service的生命周期，我们再看onStartCommand方法：






	  





    
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        onStart(intent, startId);
        return mRedelivery ? START_REDELIVER_INTENT : START_NOT_STICKY;
    }





	  







	后面的返回值，可以看看Service类的相关内容。onStartCommand方法调用了onStart方法。再跟到onStart方法：






	  





    
    @Override
    public void onStart(Intent intent, int startId) {
        Message msg = mServiceHandler.obtainMessage();
        msg.arg1 = startId;
        msg.obj = intent;
        mServiceHandler.sendMessage(msg);
    }





	  







	每次调用Context.startService(Intent intent)，都会将一个请求（Message，包含一个Intent，并有一个id）添加到消息队列（工作线程的循环队列，相关内容可以看Handler源码）。处理完所有请求后，就会停止服务。






	对于onHandleIntent(Intent intent)方法，官方注释是这样说的：






	这个方法运行在工作线程，用于处理一个请求。同一时间只有一个Intent被处理，但是处理过程发生在一个独立于其它应用逻辑的工作线程。因此，如果这个代码块要执行很长时间，它将阻塞其它在同一个IntentService上的请求，除此之外，它不会阻塞其它任何东西。






	另外，IntentService实现了一个默认的onBind方法，默认返回null。

