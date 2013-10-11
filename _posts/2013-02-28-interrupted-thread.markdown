---
author: gavin
comments: true
date: 2013-02-28 10:26:32
layout: post
slug: '%e4%b8%ad%e6%96%ad%e7%ba%bf%e7%a8%8b'
title: 中断线程
wordpress_id: 44
categories:
- Java
- 多线程
tags:
- Java
- 中断线程
---

本文的内容全部来自Core Java 

当线程的run方法执行完最后一条语句并return，或者出现了在方法中未捕获的异常时，线程将终止。 

interrupt()方法只是请求终止线程，当调用此方法时，线程的中断状态将被置位。每个线程都应该不时地检查这个标志，以判断线程是否被中断。 

当在一个被阻塞的线程（调用sleep或者wait）上调用interrupted方法时，阻塞调用将会被InterruptedException中断。  

没有任何一个语言方面的需求要求一个被中断的线程应该终止。中断一个线程不过是为了引起它的注意，被中断的线程可以决定如何响应中断。某些线程是如此重要以至于应该处理完异常后继续运行，而不是中断。更是，更普遍的情况是，线程将简单地将中断作为一个终止的请求。这种情况下线程的run方法具有如下形式： 

    public void run(){
        try{
            while(!Thread.currentThread().isInterrupted()
                    && more work to do){
                do more work
            }
        }catch(InterruptedException e){
            // thread was interrupted during sleep or wait
        }finally{
            cleanup,if required
        }
        //exiting the run method terminates the thread
    }

如果在每次迭代之后都调用sleep方法（或者其它的可中断方法），isInterrupted检测就没有用处也没有必要。如果在中断状态被置位时调用sleep方法，它不会休眠。相反，它将清除这一状态并抛出InterruptedException。因此，如果你的循环调用sleep，不要检测中断状态，而是如下所示捕获InterruptedException：

    public void run(){
        try{
            while(more work to do){
                do more work
                Thread.sleep(delay);
            }
        }catch (InterruptedException e){
            //thread was interrupted during sleep or wait
        }finally{
            cleanup,if required
        }
        //exiting the run method terminates the thread
    }

有时候InterruptedException被抑制在很低的层次上，比如：

    void mySubTask(){
        …
        try{
            sleep(dalay);
        }catch(InterruptedException e){
        
        }
        ...
    }

这时候有一种更好的解决办法：

    void mySubTask(){
        …
        try{
            sleep(dalay);
        }catch(InterruptedException e){
            Thread.currentThread().interrupt();
        }
    }

当然，最好的解决办法是：

    void mySubTask() throws InterruptedException{
        …
        sleep(dalay);
        ...
    }
