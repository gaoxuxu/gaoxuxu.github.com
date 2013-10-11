---
author: gavin
comments: true
date: 2013-02-28 10:29:17
layout: post
slug: '%e7%ba%bf%e7%a8%8b%e7%9a%84%e7%8a%b6%e6%80%81'
title: 线程的状态
wordpress_id: 47
categories:
- Java
- 多线程
tags:
- Java
- 线程状态
---

线程共有六种状态：

1. NEW（新生）：当用new操作符创建一个新线程时，该线程还没有开始运行，此时它的状态是NEW。
2. RUNNABLE（可运行的）：当线程调用start方法后，该线程即处于RUNNABLE状态。之所以是RUNNABLE，是因为此时线程可能在运行，也可能没有运行，这取决于操作系统给线程提供的时间。线程调度的细节依赖于操作系统提供的服务。抢占式调度系统给每一个可运行线程一个时间片来执行任务。当时间片用完，操作系统剥夺该线程的运行权，并给另外的线程运行机会。当选择下一个线程时，操作系统会考虑程序的优先级（协调式调度系统中，优先级没用）。
3. BLOCKED（被阻塞的）
4. WATING（等待）
5. TIMED WATING（计时等待）：当线程处于被阻塞或者等待状态时，它暂时不活动，不运行任何代码并且消耗最少的资源。直到线程调度器重新激活它：
  1. 当一个线程试图获取一个内部的对象锁（不是java.util.concurrent库中的锁），而该锁被其它线程持有，则该线程进入阻塞状态（BLOCKED）。当其它线程释放该锁，并且线程调度器允许它持有该锁时，该线程将变成非阻塞状态（RUNNABLE）。
  2. 当线程等待另一个线程通知调度器一个条件时，它进入等待状态（WATING）。在调用Object.wait方法、Thread.join方法，或者是等待java.util.concurrent库中的Lock或Condition时，就会出现这种情况。
  3. 有几种方法有一个超时参数，调用它们导致线程进入计时等待状态（TIMED WATING）。这一状态将一直保持到超时期满或者接收到适当的通知。带有超时参数的方法有Tread.sleep、Object.wait、Thread.join、Lock.tryLock以及Condition.await的计时板。
6. TERMINATED（已终止）：线程在下列两种情况下将被终止：1）因为run方法正常退出而自然死亡。2）因为一个没有捕获的异常终止了run方法而意外死亡。
