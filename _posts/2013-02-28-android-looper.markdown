---
author: gavin
comments: true
date: 2013-02-28 14:18:11
layout: post
slug: android%e4%b9%8blooper
title: Android之Looper
wordpress_id: 58
categories:
- Android
- 源码分析
tags:
- Android
- Handler
- Looper
- Message
---

Looper：循环器，是一个用于使线程运行一个消息（Message）循环的类。线程默认没有关联的消息循环，在创建它之前，在将要运行循环的线程中调用prepare方法，然后loop方法会处理消息直到循环结束。

绝大多数消息循环是通过Handler类来Looper交互的。

下面是实现了Looper的线程的一个典型例子，使用分离的prepare方法和loop方法来创建一个用于跟Looper沟通的初始Handler。

    class LooperThread extends Thread {
        public Handler mHandler;
        public void run() {
            Looper.prepare();
            mHandler = new Handler() {
                public void handleMessage(Message msg) {
                    // process incoming messages here
                }
            };
            Looper.loop();
        }
    }

先看准备工作的prepare方法：

    public static final void prepare() {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper());
    }

每条线程只能绑定一个Looper，否则会抛出RuntimeException异常。如果不调用prepare方法，sThreadLocal.get()就会返回null。注意这是个静态方法，在最后一行中绑定了一个做为Looper的线程。怎么绑定的呢，看看Looper的一个私有无参构造方法：

    private Looper() {
        mQueue = new MessageQueue();
        mRun = true;
        mThread = Thread.currentThread();
    }

构造方法里初始化了一个消息队列、一个运行状态的标志以及绑定一个线程（创建者所在的线程）。prepare方法给了一个在真正的循环开始之前，参照Looper创建一个Handler的机会。注意：在run方法中初始化Handler之前，必须调用Looper.prepare()，不然会抛出一个运行时异常，因为Handler需要一个从Looper得到的消息队列（即构造方法里面的mQueue）。调用prepare方法后一定要调用loop方法，并且调用quit方法退出循环。

接下来，看看在当前线程里运行一个消息队列的loop方法，用完后一定要调用quit方法结束循环：

	public static final void loop() {
        Looper me = myLooper();
        MessageQueue queue = me.mQueue;
        while (true) {
            Message msg = queue.next(); // might block
            if (msg != null) {
                if (msg.target == null) {
                    // No target is a magic identifier for the quit message.
                    return;
                }
                msg.target.dispatchMessage(msg);
                msg.recycle();
            }
        }
    }

高潮来了，上面说过，Looper的mQueue是Handler使用的消息队列，loop方法启动一个死循环从队列中获取数据，当队列中没有数据时，queue.next()会阻塞。当有数据时，msg.target（即Handler）就会调用dispatchMessage方法，进而调用Handler的handleCallback或者handlerMessage方法。从这些也可以知道，如果Handler是在子线程的run方法中创建（之前必须调用Looper.prepare()）的，那么这个Handler的handleMessage或者handleCallback方法就是运行在子线程的。
