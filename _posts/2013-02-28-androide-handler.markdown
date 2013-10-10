---
author: gavin
comments: true
date: 2013-02-28 14:43:50
layout: post
slug: android%e4%b9%8bhandler
title: Android之Handler
wordpress_id: 60
categories:
- Android
- 源码分析
tags:
- Android
- Handler
- Looper
- Message
---

Handler：Handler允许你发送并且处理和线程消息队列相关的消息（Message）和可运行对象。每个Handler实例都有一个单独的相关线程和这个线程的消息队列。当你创建一个新的Handler时候，它将和创建者所在线程以及此线程的消息队列绑定。从这一刻起，它将传递消息和可运行对象到消息队列，并在消息和可运行对象被传出消息队列时执行它们。  

Handler主要有两种用途：

* 把要被执行的消息和可运行对象添加到将要执行的任务清单里。
* 把要在另外一条线程上执行的动作列入自己的队列。

安排消息到任务清单，可以使用以下几种方法：post，postAtTime(Runnable,long)，postDelayed，sendEmptyMessage，sendMessage，sendMessageAtTime以及sendMessageDelayed方法。post版本的方法允许你安排Runnable对象到任务清单，这些Runnable对象将在消息队列接收到后调用它们。sendMessage版本的方法允许你安排一个Message对象到任务清单，这个Message对象包含一个将被Hanlder的handleMessage方法处理的数据包（需要在你的子类中实现handleMessage方法）。  

你在posting或者sending时，既可以允许消息循环在准备完成后立即处理任务，也可以在执行任务之前指定一个延迟时间。上述post和send系列的最后两个方法可以设置超时、空转和定时行为。

当你的应用程序创建一个进程的时候，这个进程的主线程就专门运行一个负责管理顶级程序对象（activities，broadcast receivers等等）和所有由这些对象创建的窗口的消息循环。你可以创建自己的线程，并且通过一个Handler和应用的主线程沟通。这样做跟之前调用post或者sendMessage一样，只不过是在你的子线程中调用的。给定的Runnable对象或者Message将被添加到Hanlder的消息队列，并且在适当的时候处理它们。

Hanlder默认的构造方法，将这个Handler跟当前线程中的队列关联了起来：

    Handler() {
        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw newRuntimeException("Can't create handler inside thread that has not called Looper.prepare()"); 
        }
        mQueue = mLooper.mQueue; 
        mCallback = null;
    }

Handler必须要有一个消息队列，不然它没法接受消息。在线程初始化Handler之前，必须调用Looper.prepare()，详细内容请参考[《Android之Looper》。 ](http://my.oschina.net/u/272863/blog/93721) 

初始化Handler时，还可以指定Looper和Callback，前者可以使消息循环在Looper相关的线程中运行，后者可以使你避免重写Handler。

Handler的obtainMessage系列方法都是从全局的消息池中拿Message，如果池中没有，就new一个返回。

post系列方法，都是封装的对应的send系列方法（将Runnable对象（msg.callback）和一个Object对象（即msg.object，如果有的话）通过getPostMessage方法拿到一个Message，然后send）。

    private final Message getPostMessage(Runnable r, Object token) {
        Message m = Message.obtain();
        m.obj = token;
        m.callback = r;
        return m;
    }

send系列方法，包装的都是sendMessageAtTime方法（sendMessageAtFrontOfQueue方法也是它的一个变种）。在sendMessageAtTime方法中，第一个参数是将被传递的Message对象，第二个是传递时的时间（可以猜想delayed系列方法都是当前时间加上延迟时间，事实证明这种猜想是正确的）。

    public boolean sendMessageAtTime(Message msg, long uptimeMillis){
        boolean sent = false;
        MessageQueue queue = mQueue;
        if (queue != null) {
            msg.target = this;
            sent = queue.enqueueMessage(msg, uptimeMillis);
        } else {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
        }
        return sent;
    }

在这个方法中，将Message排入消息队列（队列中按照uptimeMillis排序，而sendMessageAtFrontOfQueue方法中将它设为0，所以sendMessageAtFrontOfQueue方法传递的Message将在Looper的loop方法循环的下一次循环中立即执行）。enqueueMessage方法的排序部分如下：

    synchronized (this) {
        if (mQuiting) {
            RuntimeException e = new RuntimeException(
                    msg.target + " sending message to a Handler on a dead thread");
            Log.w("MessageQueue", e.getMessage(), e);
            return false;
        } else if (msg.target == null) {
            mQuiting = true;
        }
        msg.when = when;
        //Log.d("MessageQueue", "Enqueing: " + msg);
        Message p = mMessages;
        if (p == null || when == 0 || when < p.when) {
            msg.next = p;
            mMessages = msg;
            this.notify();
        } else {
            Message prev = null;
            while (p != null && p.when <= when) {
                prev = p;
                p = p.next;
            }
            msg.next = prev.next;
            prev.next = msg;
            this.notify();
        }
    }

将Message排入消息队列后的事情，是由Looper来完成的，详见[《Android之Looper》](http://my.oschina.net/u/272863/blog/93721)中的loop方法。在Looper的loop方法中，调用了msg.target（即Handler，见sendMessageAtTime方法）的dispatchMessage方法。

    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }

第一个判断，msg.callback即为post系列方法传递到的Runnable对象。

    private final void handleCallback(Message message) {
        message.callback.run();
    }

第二个mCallback.handleMessage方法，可以让你避免在自己的Handler子类中重写handleMessage方法。

还有remove系列方法，就是根据条件从消息队列中移除相关的Message。
