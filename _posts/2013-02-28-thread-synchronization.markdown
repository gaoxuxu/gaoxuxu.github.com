---
author: gavin
comments: true
date: 2013-02-28 11:05:44
layout: post
slug: '%e7%ba%bf%e7%a8%8b%e5%90%8c%e6%ad%a5'
title: 线程同步
wordpress_id: 51
categories:
- Java
- 多线程
tags:
- Java
- 线程同步
---

##竞争条件

在大多数实际的多线程应用中，两个或两个以上的线程需要共享对同一数据的存取。当两个线程存取相同的对象，并且每一个线程都调用了一个修改该对象状态的方法的时候，根据各线程访问数据的次序，可能会产生讹误的对象。这样的情况通常称为“竞争条件”。为了避免多线程引起的对共享数据的讹误，就必须学习如何“同步存取”。

##锁对象

从Java SE 5.0开始，有两种机制防止代码块受并发访问的干扰。即Java语言关键字synchronized和Java SE 5.0引入的ReentrantLock类。synchronized关键字自动提供一个锁以及相关的“条件”（Condition），对于大多数需要显式锁的情况，这是很便利的。使用ReentrantLock保护代码块的基本结构如下：

    myLock.lock();//a ReentrantLock object
    try{
        critical section
    }finally{
        myLock.unlock();
        //make sure the lock is unlocked even if an exception is thrown
    }

这一结构确保任何时刻只有一个线程进入临界区。一旦一个线程封锁了锁对象，其它任何线程都无法通过lock语句。当其它线程调用lock时，它们被阻塞，直到第一个线程释放锁对象。

锁是可重入的（若一个程序或子程序可以“安全的被并行执行”，则称其为“可重入”的），因为线程可以重复地获得已经持有的锁。锁保持一个持有计数（hold count）来跟踪对lock方法的嵌套调用。线程在每一次调用lock都要调用unlock来释放锁。由于这一特性，被一个锁保护的代码可以调用另一个使用相同的锁的方法。

通常，可能想要保护需若干个操作来更新或检查共享对象的代码块。要确保这些操作都完成后，另一个线程才能使用相同对象。

要留心临界区中的代码，不要因为异常的抛出而跳出了临界区。如果在临界区代码结束之前抛出了异常，finally子句将释放锁，但会使对象处于一种受损状态。

另外，ReentrantLock还提供一个带有公平策略的构造方法——ReentrantLock(boolean fair)。一个公平锁偏爱等待时间最长的线程。但是，这一公平的保证将大大降低性能。所以，默认情况下，锁没有被强制为公平的。不过，遗憾的是，即使使用公平锁，也无法确保线程调度器是公平的。

##条件对象

通常，线程进入临界区，却发现在某一条件满足之后它才能执行。这时候，就需要一个条件对象来管理那些已经获得了一个锁但是却不能做有用工作的线程（由于历史原因，条件对象经常被称为条件变量（conditional variable））。  

一个锁对象可以有一个或多个相关的条件对象，可以使用newCondition方法获得一个条件对象。习惯上给每一个条件对象命名为可以反映它所表达的条件的名字。

等待获得锁的线程和调用await方法的线程存在本质上的不同。一旦一个线程调用await方法，它进入该条件的等待集。当锁可用时，该线程不能马上解除阻塞。相反，它处于阻塞状态，直到另一个线程调用同一条件上的signalAll方法时为止。

当另一条线程调用signalAll方法时，这一调用重新激活了因为这一条件而等待的所有线程。当这些线程从等待集当中移出时，它们再次成为可运行的，调度器将再次激活它们。同时，它们将试图重新进入该对象。一旦锁成为可用的，他们中的某个将从await调用返回，获得该锁并从阻塞的地方继续执行。此时，线程应该再次测试该条件。由于无法确保该条件被满足——signalAll方法仅仅是通知正在等待的线程：此时有可能已经满足条件，值得再次去检测该条件。

通常，await的调用应该在如下形式的循环体中： 

    while(!(Ok to proceed))
        condition.await();

至关重要的是最终需要某个其它线程调用signalAll方法。当一个线程调用await时，它没有办法重新激活自身。它寄希望于其它线程，而如果没有其它线程来重新激活等待的线程的话，它就永远不在运行了。这将导致令人不快的死锁（deathlock）现象。如果所有其它线程被阻塞，最后一个活动线程在解除其它线程的阻塞状态之前就调用了await方法，那么它也就被阻塞了。没有任何线程可以解除其它线程的阻塞，那么该程序就挂起了。

从经验上讲，应该在对象的状态有利于等待线程的方向改变时调用signalAll方法。

注意：调用signalAll方法不会立即激活一个等待线程。它仅仅解除等待线程的阻塞，以便这些线程可以在当前线程退出同步方法后，通过竞争实现对对象的访问。

另一个方法signal，则是随机解除等待集中某个线程的阻塞状态。这比解除所有线程的阻塞更加有效，但也存在危险。如果随机选择的线程发现自己仍然不能运行，那么它再次被阻塞。如果没有其它线程再次调用signal，那么系统就死锁了。

##synchronized关键字

我们先总结一下锁和条件的关键之处：

1. 锁用来保护代码片段，任何时刻只能有一个线程执行被保护的代码。
2. 锁可以管理试图进入被保护代码段的线程。
3. 锁可以拥有一个或多个相关的条件对象。
4. 每个条件对象管理那些已经进入被保护的代码段但还不能运行的线程。

Lock和Condition接口被添加到Java SE 5.0中，这也向程序设计人员提供了高度的封锁机制。然而，大多数情况下，并不需要那样的控制，并且可以使用一种嵌入到Java语言内部的机制。从1.0版开始，Java中的每一个对象都有一个内部锁。如果一个方法用synchronized关键字声明，那么对象的锁将保护整个方法。也就是说，要调用该方法，线程必须获得内部的对象锁。

换句话说， 

    public synchronized void method(){
        method body
    }

等价于 

    public void method(){
        this.intrinsicLock.lock();
        try{
            method body
        }finally{
            this.intrinsicLock.unlock();
        }
    }

内部对象锁只有一个相关条件。wait方法添加一个线程到等待集中，notifyAll/notify方法解除等待线程的阻塞状态。换句话说，调用wait或者notifyAll方法等价于：

    intrinsicCondition.await();
    intrinsicCondition.signalAll();

可以看出，使用synchronized关键字来编写代码要简洁的多。当然，要理解这些代码，就必须了解每一个对象有一个内部锁，并且该锁有一个内部条件。由锁来管理那些试图进入synchronized方法的线程，由条件来管理那些调用wait的线程。

将静态方法声明为synchronized也是合法的。如果调用这种方法，该方法获得相关的类对象（Class）的内部锁。因此，没有其它线程可以调用同一个类的这个或任何其它的同步静态方法。

内部锁和条件存在一些局限，包括：

1. 不能中断一个正在试图获得锁的线程。
2. 试图获得锁时不能设置超时。
3. 每个锁仅有单一的条件，可能是不够的。

在代码中选择使用Lock和Condition对象还是同步方法的一些建议：

1. 最好既不使用Lock/Condition也不使用synchronized关键字。在许多情况下你可以使用java.util.concurrent包中的一种机制（阻塞队列），它会为你处理所有的加锁。
2. 如果synchronized关键字适合你的程序，那么请尽量使用它，这样可以减少编写的代码数量，减少出错的几率。
3. 如果特别需要Lock/Condition结构提供的独有特性时，才使用Lock/Condition。

##同步阻塞

每个Java对象都有一个锁。线程可以通过调用同步方法获得锁，也可以通过进入一个同步阻塞，获得该对象的锁：

    synchronized(obj){
        critical section
    }

有时程序员使用一个对象的锁来实现额外的原子操作，实际上被称为客户端锁定（client-side locking）。但是客户端锁定是非常脆弱的，通常不推荐使用。因为这种方法完全依赖于这样一个事实，obj对自己的所有可修改方法都使用内部锁。

##监视器概念

锁和条件是线程同步的强大工具，但是严格地讲，它们不是面向对象的。多年来，研究人员努力寻找一种方法，可以在不需要程序员考虑如何加锁的情况下，就可以保证多线程的安全性。最成功的解决方案之一是监视器（monitor），这一概念最早是由Per Brinch Hansen和Tony Hoare 在20世纪70年代提出的。用Java的术语讲，监视器具有如下特性：

1. 监视器是只包含私有域的类。
2. 每个监视器类的对象有一个相关的锁。
3. 使用该锁对所有的方法进行加锁。换句话说，如果客户端调用obj.method，那么obj对象的锁是在方法调用开始时自动获得，并且当方法返回时自动释放该锁。因为所有的域是私有的，这样的安排可以确保一个线程在对对象操作上，没有其它线程能访问该域。
4. 该锁可以有任意多个相关条件。

监视器的早期版本只有单一的条件，不使用任何显式的条件变量。然而，研究表明盲目地重新测试条件是低效的。显式的条件变量解决了这一问题，每一个条件变量管理一个独立的线程集。

Java设计者以不是很精确的方式采用了监视器概念，Java中的每一个对象有一个内部的锁和内部的条件。如果一个方法用synchronized关键字声明，那么，它表现的就像是一个监视器方法。通过调用wait/notifyAll/notify来访问条件变量。

然而，在下面三个方面Java对象不同于监视器，从而使得线程的安全性下降：

1. 域不要求是私有的。
2. 方法不要求必须是synchronized。
3. 内部锁对客户是可用的。 

##Volatile域

有时，仅仅为了读写一个或两个实例域就使用同步，显得开销过大了。那么，什么地方能出错呢？遗憾的是，使用现代的处理器与编译器，出错的可能性很大：

1. 多处理器的计算机能够暂时在寄存器或本地内存缓冲区中保存内存中的值。结果是，运行在不同处理器上的线程可能在同一个内存位置取到不同的值。
2. 编译器可以改变指令执行的顺序以使吞吐量最大化。这种顺序上的变化不会改变代码语义，但是编译器假定内存的值仅仅在代码中有显式的修改指令时才会改变。然而，内存的值可以被另一个线程改变！

如果你使用锁来保护可以被多个线程访问的代码，那么可以不考虑这种问题。编译器被要求通过在必要的时候刷新本地缓存来保持锁的效应，并且不能不正当地重新排序指令。详细解释参见JSR 133的Java内存模型和线程规范（[http://www.jcp.org/en/jsr/detail?id=133](http://www.jcp.org/en/jsr/detail?id=133)）。

Brian Goetz给出了下述“同步格言”：如果向一个变量写入值，而这个变量接下来可能会被另一个线程读取，或者，从一个变量读值，而这个变量可能是之前被另一个线程写入的，此时必须使用同步。

volatile关键字为实例域的同步访问提供了一种免锁机制。如果声明一个域为volatile，那么编译器和虚拟机就知道该域是可能被另一个线程并发更新的。

例如，假定一个对象有一个布尔标记done，它的值被一个线程设置却被另一个线程查询，如之前所述，你可以使用锁： 

    public synchronized boolean isDone(){ return done; }
    public synchronized void setDone(){ done = true; }
    private boolean done;

或许使用内部锁不是个好主意。如果另一个线程已经对该对象加锁，isDone和setDone方法可能阻塞。如果注意到这个方面，一个线程可以为这一变量使用独立的Lock。但是，这也会带来许多麻烦。

在这种情况下，就可以将域声明为volatile： 

    public boolean getDone(){ return done; }
    public void setDone(){ done = true; };
    private volatile boolean done;

这地方需要注意的一点是，Volatile不能提供原子性。例如，方法 

    public void flipDone(){ done = !done; }//not atomic

不能确保改变域中的值。

在这样一种非常简单的情况下，存在第三种可能性，使用AtomicBoolean。这个类有方法get和set，且确保是原子的（就像它们是同步的一样）。该实现使用有效的机器指令，在不使用锁的情况下确保原子性。在java.util.concurrent.atomic中有许多包装器类用于原子的整数、浮点数、数组等。这些类是为编写并发实用程序的系统程序员提供使用的，而不是应用程序员。

总之，在以下三个条件下，域的并发访问是安全的：

1. 域是final的，并且在构造器调用完成之后被访问。
2. 对域的访问由公有的锁进行保护。
3. 域是volatile的。

语言的设计者试图在优化使用volatile域的代码的性能方面给实现人员留有余地。但是，旧规范太复杂，实现人员难以理解，这带来了混乱和非预期的行为。例如，不可变对象不是真的不可变。

##死锁

锁和条件不能解决多线程中的所有问题，比如死锁。在一个程序中，所有线程都被阻塞，这样的状态被称为死锁。遗憾的是，Java编程语言中没有任何东西可以避免或打破这种死锁现象。必须仔细设计程序，以确保不会出现死锁。

##锁测试与超时

线程在调用lock方法来获得另一个线程锁持有的锁的时候，很可能发生阻塞。应该更加谨慎地申请锁。tryLock方法试图申请一个锁，在成功获得锁后返回true，否则，立即返回false，而且线程可以立即离开去做其它事情。

    if(myLock.tryLock()){
        // now the thread owns the lock
        try {
            ...
        }finally{
            myLock.unlock();
        }
    }else{
        do something else
    }

可以调用tryLock时，使用超时参数，例如： 

    if(myLock.tryLock(100,TimeUnit.MILLISECONDS))......

TimeUnit是一个枚举类型，可以取的值包括SECONDS、MILLISECONDS、MICROSECONDS和NANOSECONDS。

lock方法不能被中断。如果一个线程在等待获得一个锁时被中断，中断线程在获得锁之前一直处于阻塞状态。如果出现死锁，lock方法就无法终止。

然而，如果调用带有超时参数的tryLock，那么如果线程在等待期间被中断，将抛出InterruptedException异常。这是一个非常有用的特性，因为允许程序打破死锁。

也可以调用lockInterruptibly方法。它就相当于一个超时设为无限的tryLock方法。

在等待一个条件时，也可以提供一个超时：

    myCondition.await(100,TimeUnit.MILLISECONDS);

如果一个线程被另一个线程通过调用signalAll或signal激活，或者超时时限已达到，或者线程被中断，那么await方法将返回。

如果等待的线程被中断，await方法将抛出一个InterruptedException异常。在你希望出现这种情况时线程继续等待，可以使用awaitUninterruptibly方法代替await。

##读/写锁

java.util.concurrent.locks包定义了两个锁类，除了前面的ReentrantLock类，还有ReentrantReadWriteLock类。如果很多线程从一个数据结构读取数据而很少线程修改其中数据的话，后者是十分有用的。在这种情况下，允许对读者线程共享访问是合适的。当然，写者线程依然必须是互斥访问的。

下面是使用读/写锁的必要步骤：

1. 构造一个ReentrantReadWriteLock对象：  
<code>private ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();</code>
2. 抽取读锁和写锁：  
<code>private Lock readLock = rwl.readLock();  
private Lock writeLock = rwl.writeLock();</code>
3. 对所有的访问者加读锁。
4. 对所有的修改者加写锁。

##为什么弃用stop和suspend方法？

初始的Java版本定义了一个stop方法用来终止一个线程，以及一个suspend方法用来阻塞一个线程
直至另一个线程调用resume。stop和suspend方法有一个共同点：都试图控制一个给定线程的行为。

从Java1.2开始就弃用了这两个方法。stop方法天生就不安全，而经验证明suspend方法会经常导致死锁。

首先来看stop方法，该方法终止所有未结束的方法，包括run方法。当线程被终止，立即释放被它锁住的所有对象的锁。这会导致对象处于不一致的状态。例如，假定一个银行转账的线程TransferThread，在从一个账户向另一个账户转账的过程中被终止，钱款已经转出，却没有转入目标账户，现在银行对象就被破坏了，这种破坏会被其他尚未停止的线程观察到。

当线程要终止另一个线程时，无法知道什么时候调用stop方法是安全的，什么时候会导致对象被破坏。因此，该方法被弃用了。在希望停止线程的时候应该中断线程，被中断的线程会在安全的时候停止。

接下来，看看suspend方法有什么问题。与stop方法不同，suspend不会破坏对象。但是如果用suspend挂起一个持有一个锁的线程，那么，该锁在恢复之前是不可用的。如果调用suspend方法的线程试图获得同一个锁，那么程序死锁：被挂起的线程等待恢复，而将其挂起的线程等待获得锁。

如果想安全地挂起线程，引入一个变量suspendRequested并在run方法的某个安全的地方测试它，安全的地方是指该线程没有封锁其它线程需要的对象的地方。当该线程发现suspendRequested变量已经设置，将会保持等待状态直到它再次获得为止。下面的代码框架实现这一设计：

    public void run(){
        while(...){
            ...
            if(suspendRequested){
                suspendLock.lock();
                try{
                    while(suspendRequested)
                        suspendCondition.await();
                }finally{
                    suspendLock.unlock();
                }
            }
        }
    }
    
    public void requestSuspend(){
        suspendRequested = true;
    }
    
    public void requestResume(){
        suspendRequested = false;
        suspendLock.lock();
        try{
            suspendCondition.signalAll();
        }finally{
            suspendLock.unlock();
        }
    }
    private volatile boolean suspendRequested = false;
    private Lock suspendLock = new ReentrantLock();
    private Condition suspendCondition = suspendLock.newCondition();
