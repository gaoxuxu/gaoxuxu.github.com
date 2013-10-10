---
author: gavin
comments: true
date: 2013-02-28 10:14:07
layout: post
slug: java-lang-thread
title: java.lang.Thread
wordpress_id: 42
categories:
- Java
- 多线程
tags:
- Java
- Thread
---


	线程是执行并发任务的最小单元。它有单独的用于方法调用、参数和本地变量的堆栈。每一个已启动的虚拟机实例至少有一条主线程在运行；通常情况下，会有另外几条线程去进行一些事务操作。有时候为了特殊的目的，应用程序也会添加一条子线程。






	同一个VM中的线程通过使用共享对象和监控跟它们相关的一些对象来相互影响和同步。子线程之间的同步也可以使用同步方法和Object类中的一些API（notify()、notifyAll()、wait()...）。






	程序代码中，执行线程的最基本方法有两种。一是提供一个新的继承自Thread的类，并且重写它的run()方法。另一种方法是，在创建线程的时候提供一个Runnable接口的实现。






	在上述两种情况下，实际执行Thread的方法必须是start()方法。






	每条线程都有一个数字表示的优先级（最小为1，最大为10，主线程默认是5，子线程默认继承父线程的优先级），优先级基本上决定了此线程能分配到的CPU处理时间的总额。优先级可以使用setPriortity()方法来设置（最大不超过所属ThreadGroup的优先级）。一条线程也可以是Daemon Thread，这使得它可以在后台运行。后者还会影响VM的终止行为：只要有非Daemon Thread运行，虚拟机就不会停止。






	线程的状态（同时只能有下列枚举中的一种状态）：






	  





    
    public enum State{
        NEW,RUNNABLE,BLOCKED,WAITING,TIMED_WAITING,TERMINATED
    }





	线程的id = ++Thread.count，所以它是独一无二的。 






	线程的name，如果未指定，是"Thread-"+id。






	UncaughtExceptionHandler类可以捕获当前线程抛出的未经捕获的异常。






	在创建线程时，如果不指定ThreadGroup，则新线程归属于创建者线程所在的ThreadGroup。






	主要方法：






	public void interrupt()：给这条线程发送一个中断请求。如果调用者是currentThread()，方法checkAccess()就会被调用（如果有SecurityManager的话）。这可能会导致一个SecurityException被抛出，更进一步的行为取决于线程当前的状态：






	如果线程阻塞于下面情况：1）Object的某个wait()方法；2）线程的join()方法；3）线程的sleep()方法。线程将会被唤醒，它们的中断状态将会被清除，并且它们会接收到一个InterruptedException。






	如果线程阻塞于java.nio.channels.InterruptiableChannel的I/O操作，将会设置它们的中断状态为true，并且接收到一个java.nio.channels.ClosedByInterruptException。同时，这个通道将被关闭。






	如果线程阻塞于java.nio.channels.Selector，将会设置它们的中断状态为true并且立即返回。在这种情况下不会抛出任何异常。 






	public static boolean interrupted()：返回一个表示currentThread()是否有一个中断请求的布尔值。它会清除这条线程的中断标记，这时候它会有一个副作用（也就是，如果连续调用两次此方法，如果第一次返回true，则第二次会返回false）。






	public final boolean isAlive()：返回一个表示线程生命状态的布尔值。如果接收者已经开始(started)并且仍然在运行(还没有死亡)，则返回true。当接收者还没有开始，或者它已经开始，而且已经执行结束并且死亡，则返回false。






	public final boolean isDaemon()：返回一个表示线程是否Daemon Thread的布尔值。Daemon Thread的运行取决于非Daemon Thread（即User Thread）的运行状态。当最后一条User Thread结束的时候，无论是否有Daemon Thread在运行，整个程序都会结束。






	public boolean isInterrupted()：返回一个表示接收者是否有一个中断请求的布尔值。






	public final void join()：阻塞current Thread直到接收者执行完毕并且死亡。






	  





    
    public final void join() throws InterruptedException {
        VMThread t = vmThread;
        if (t == null) {
            return;
        }
        synchronized (t) {
            while (isAlive()) {
                t.wait();
            }
        }
    }





	  




public final void join(long millis)：阻塞current Thread直到下列两种情况满足至少一种：1）接收者执行完毕并且死亡；2）指定的时间终止。 



	public final void join(long millis, int nanos)：阻塞current Thread直到下列两种情况满足至少一种：1）接收者执行完毕并且死亡；2）指定的时间终止。参数nanos：纳秒级别的精确度。






	public void run()：调用接收者的Runnable对象的run()方法，如果没有设置Runnable对象，则不做任何事。不要直接调用Thread或者Runnable的run方法。直接调用run方法，只会执行同一个线程中的任务，而不会启动新线程。






	public void setContextClassLoader(ClassLoader cl)：设置接收者的context ClassLoader。






	public final void setDaemon(boolean isDaemon)：设置接收者是否为Daemon Thread，这个方法必须在start()方法之前调用。






	public static void setDefaultUncaughtExceptionHandler(UncaughtExceptionHandler handler)：设置默认的未捕获异常处理器，这个处理器将会在有未处理的异常导致线程死亡时调用。






	public final void setName(String threadName)：设置线程的名字。






	public final void setPriority(int priority)：设置线程的优先级。注意：最终的优先级也许不是我们传入的值，因为它是依赖于接收者所在的ThreadGroup的。线程的优先级不能设置的比它所在的ThreadGroup的maxPriority()还高。






	public void setUncaughtExceptionHandler(UncaughtExceptionHandler handler)：设置未捕获异常处理器，这个处理器将会在有未处理的异常导致线程死亡时调用。 






	public static void sleep(long time)：使发送这条消息的线程睡眠指定的时间间隔（给定的毫秒）。这个方法的精确度无法保证，线程的实际睡眠时间可能多于或少于指定的时间。






	public static void sleep(long millis, int nanos)：使发送这条消息的线程睡眠指定的时间间隔（给定的毫秒和纳秒）。这个方法的精确度无法保证，线程的实际睡眠时间可能多于或少于指定的时间。 






	public synchronized void start()：启动一条新线程，接收者线程将会调用它自己的run()方法。






	public String toString()：返回一个关于线程的简洁的、人容易理解的描述。包括这个线程的名字、优先级和ThreadGroup的名字。






	public static void yield()：使用此方法将调用线程的执行时间给予另外一条准备运行的线程。实际情况依赖于实现（如果有其它的可运行（RUNNABLE）线程具有至少与此线程同样高的优先级，那么这些线程接下来会被调度）。注意，这是一个静态方法。






	public static boolean holdsLock(Object object)：表示current Thread是否有一个在指定对象上的监控锁。






	public static int activeCount()：返回正在运行的线程所在的ThreadGroup以及它的子Group中处于活动状态的线程数量。






	public final void checkAccess():用于检查SecurityManager是否允许操作。如果没有安装SecurityManager，则不进行任何操作。如果安装了SecurityManager的话，会调用此SecurityManager的checkAccess()方法。当SecurityManager已安装，并且不允许访问此线程时，抛出SecurityException。






	public static Thread currentThread()：返回调用者的线程，即当前线程。






	public static void dumpStack()：将此线程当前堆栈的标准错误流打印到一个文本。






	public static int enumerate(Thread[] threads)：将当前线程所在的ThreadGroup以及它的子ThreadGroup中的所有线程复制到传入的线程数组中。如果传入的这个线程数组太小，额外的线程将不会被复制进去。返回值是拷贝的线程数目。






	public static Map<Thread, StackTraceElement[]> getAllStackTraces()：返回所有当前存活的线程的堆栈轨迹。






	public ClassLoader getContextClassLoader()：返回此线程的上下文类加载器。如果满足以下条件，一个RuntimePermission("getClassLoader")的安全检查将在第一时间被执行：1）当前有一个SecurityManager；2）调用者的类加载器不为空；3）调用者的类加载器与被请求的上下文类加载器不一样，并且两者不是一个原型。






	public static UncaughtExceptionHandler getDefaultUncaughtExceptionHandler()：返回默认的未捕获异常处理器。






	public long getId()：返回当前线程的ID，这个ID是在线程创建的时候指定的lang型数值，这个值是独一无二的，并且在线程的生命周期中不能更改。如果线程终止，这个值可能被重用。






	public final String getName()：返回线程的名字。






	public final int getPriority()：返回线程的优先级。






	public StackTraceElement[] getStackTrace()：返回描述此线程当前执行状态的堆栈轨迹。返回结果之前会先检查RuntimePermission("getStackTrace")。






	public State getState()：返回线程当前的状态，这个方法经常被用于监控的目的。






	public final ThreadGroup getThreadGroup()：返回此线程归属的ThreadGroup。






	public UncaughtExceptionHandler getUncaughtExceptionHandler()：返回此线程的未捕获异常处理器。如果没有明确指定，则返回ThreadGroup的处理器（其实是ThreadGroup，因为ThreadGroup实现了UncaughtExceptionHandler接口）。如果此线程已经终止，则返回null。

