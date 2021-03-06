---
author: gavin
comments: true
date: 2013-03-11 17:40:19
layout: post
slug: android%e4%b9%8b%e8%bf%9b%e7%a8%8b%e5%92%8c%e7%ba%bf%e7%a8%8b
title: Android之进程和线程
wordpress_id: 112
categories:
- Android
tags:
- Android
- Process
- Thread
- 单线程模型
- 线程
---

###简介

当一个应用组件启动时，如果当前应用中没有其它正在运行的组件的话，Android系统就会为这个应用启动一个进程和一个单独的执行线程。默认情况下，同一个应用中的所有组件都运行在同一个进程和线程（主线程）中。不过，也可以让同一个应用中的不同组件运行在不同的进程，还可以在任意进程中创建另外的线程。

###进程

一般来说，大多数应用不需要改变应用程序进程的默认状态。不过，如果想要完全控制一个确定的组件所属的进程，可以通过配置manifest文件来达成目标。

manifest文件中的所有组件元素（&lt;activity>、&lt;service>、&lt;receiver>和&lt;provider>）都支持android:process属性，设置这个属性可以指定当前组件运行时所在的进程。通过设置android:process属性也可以让使用同一个证书签名的不同应用的组件在同一个进程中运行，这个进程提供一个共享的Linux User ID。

&lt;application>元素也支持android:process属性，使用它可以设置所有组件的默认值。

当系统内存不足并且其它进程需要更好地为用户服务时，Android可能会在某个时间点关闭一个进程。在这个进程中运行的应用组件都会被销毁。

**进程的生命周期：**

Android系统会尽可能长时间地保留应用进程，除非要为新的或更重要的进程回收内存。为了确定哪个进程要保留以及哪个进程要被杀死，系统会将所有进程放入一个基于进程中组件运行状态的“重要性等级”列表中。重要性最低的进程首先被淘汰，然后淘汰重要性次低的进程，等等。直到恢复了足够的系统资源。

下面是重要性等级的五个级别，重要性最高的进程会被最后杀死。

******1.前台进程：**

这种进程是用户最需要的，如果一个进程的状态符合下面任意一项，那么这个进程就被认为是前台进程：

1. 进程中驻留着正在与用户交互的Activity（Activity的onResume()方法被调用）。
2. 进程中有一个Service和正在与用户交互的Activity绑定。
3. 进程中有一个前台服务（这个Service调用了startForeground()）。
4. 进程中有一个Service正在执行生命周期回调（onCreate()、onStart()或onDestroy()）。
5. 进程中有一个BroadcastReceiver正在执行它的onReceive()方法。

一般来说，一定时间只存在很少的前台进程。前台进程只有在内存极低时，才会被当作最后手段杀死。通常来说，在那个时候，设备达到了一个内存分页状态，为了用户界面的响应会杀死一些前台进程。

******2.可见进程：**

这种进程没有任何前台组件，但是仍然对用户可见。在下面两种情况下进程会被认为是可见的：

1. 进程中某个Activity不在前台，但是仍然对用户可见（它的onPause()被调用）。
2. 进程中有一个Service与一个可见的（或前台）Activity绑定。

可见进程被认为是非常重要的，一般不会被杀死，除非内存不足以所有前台进程运行。

**3.Service进程：**

这种进程中有一条由startService()方法启动，并且不属于以上两种情况所列类型的Service。服务进程不会与用户可以看到的任何事物有直接联系，但是它们的工作却是用户会经常注意的（例如在后台播放音乐或者从网络下载数据），因此系统会保持它们运行，除非内存不足以支撑所有的前台和可见进程。  

**4.后台进程：**

这种进程包含一个对用户不可见的Activity（Activity的onStop()方法已经被调用）。这种进程不会对用户的使用产生任何影响，系统可以在任意时刻杀死它们并回收内存。通常会存在很多后台进程，系统将它们保存在一个LRU列表中以保证最近加入的进程会被最后一个杀死。当一个Activity正确地实现了它的生命周期，并保存了自己的状态时，杀死它所在的进程对用户来说没有任何效果，因为当用户再次回到这个Activity时，这个Activity会恢复它所有的可视状态。  

**5.空进程：**

这种进程中没有任何存活的应用组件。保持这种进程存活的唯一目的就是为了缓存，它可以提高组件下次运行时的启动时间。系统经常会杀死这种进程来平衡进程缓存和底层内核缓存之间的整体系统资源。

Android系统通过权衡进程中当前存活的组件的重要性来将进程排入最高的那个级别。例如，如果进程有一个Service和一个可见的Activity，系统就会认为这个进程是一个可见进程，而不是服务进程。

另外，一个进程的排行可能会由于另外一个依赖于它的进程而增加。这是因为为其它进程提供服务的进程排行不会低于被服务的那个进程。例如，如果A进程中的一个内容提供者为B进程中的一个客户端提供服务，或者A进程中的一个Bound Service与进程B中的一个组件绑定，那么A进程的重要性至少与B进程相同。

由于运行一个Service的进程排行高于Activity已经进入后台的进程，所以如果要在Activity中做一个长时间运行的操作的话，应该最好为这个操作启动一个Service，而不是简单地创建一条工作线程——特别是这个操作可能比Activity更持久的情况下。例如，Activity要向服务器上传一张图片的话，应该启动一个服务来执行上传任务，在这种情况下，就算用户离开Activity，任务也会在后台继续。无论对Activity做了什么，使用Service能保证操作至少有服务进程级别的优先级。这也跟广播接受者使用服务而不是简单地把耗时操作放入线程中的原因。  

###线程

一个应用启动后，系统就会为应用创建一个名为“main”的执行线程。这个线程非常重要，因为它负责为适当的用户界面调度事件，包括绘制事件。它也是应用与Android UI工具包中的组件发生交互所在的线程，因此，main线程也被称为UI线程。

系统不能为组件的实例创建不同的线程。所有运行在同一个进程中的组件都在UI线程中初始化，系统对每个组件的调用都会经过UI线程的调度。所以，对系统回调方法的回应也发生在进程的UI线程中。

在这种情况下，当用户触摸屏幕上的一个按钮时，应用的UI线程会为这个Widget调度触摸事件，这个触摸事件包括将按钮的状态设置为按下并且向事件队列发送一个invalidate请求。UI线程会从队列中取出请求并通知这个Widget重绘自己。

当应用执行对用户来说非常细致的工作时，这种单线程模型可能产生不良的影响。具体来说，如果在UI线程中执行一个长时间的操作，比如访问网络或者查询数据库时，这种操作会导致阻塞整个UI。当线程阻塞时，就不能调度任何事件，包括绘制事件。从用户的角度来看，就是应用程序挂起。更糟的是，如果UI线程被阻塞大约5秒，系统就会为用户弹出臭名昭著的ANR对话框。然后用户可能决定退出并卸载这个让他不愉快的应用。

另外，Android UI工具包不是线程安全的。所以，必须在UI线程中处理所有对用户界面的操作。因此，对于单线程模型有两条最基本的规则：

1. 不能阻塞UI线程。
2. 不要在UI线程以外访问Android UI工具包。

**工作线程：**

如果执行的操作不是瞬时的，就应该将它们放到单独的线程（后台线程或工作线程）中执行。

下面是一个例子，当监听到点击事件后，就在一个单独的线程中下载图片并交给一个ImageView显示：

    public void onClick(View v) {
        new Thread(new Runnable() {
            public void run() {
                Bitmap b = loadImageFromNetwork("http://example.com/image.png");
                mImageView.setImageBitmap(b);
            }
        }).start();
    }

这段代码看起来会正常运行，但是由于它没有遵守单线程模型的第二条规则：不要在UI线程以外访问Android UI工具包，所以可能导致未定义的和意外的行为，这种行为追查起来可能是非常困难而且耗时的。

要解决这个问题，Android提供了以下几种从其它线程访问UI线程的途径：  

1. Activity.runOnUiThread(Runnable)
2. View.post(Runnable)
3. View.postDelayed(Runnable,long)

例如，可以使用View.post(Runnable)方法来修复上面的代码：

    public void onClick(View v) {
        new Thread(new Runnable() {
            public void run() {
                final Bitmap bitmap = loadImageFromNetwork("http://example.com/image.png");
                mImageView.post(new Runnable() {
                    public void run() {
                        mImageView.setImageBitmap(bitmap);
                    }
                });
            }
        }).start();
    }

问题似乎完美解决了，然而，由于操作的复杂性的增加，这种代码也会变得非常复杂并且难以维护。为了处理与工作者线程之间更复杂的交互，应该考虑在工作者线程中使用一个Handler处理来自UI线程的消息。可能最好的办法是继承AsyncTask类，它简化了UI与工作者线程交互的过程。

**使用AsyncTask：**

AsyncTask允许在用户界面上执行一个同步操作。它在工作者线程中执行阻塞操作并在UI线程中返回结果，而不需要自己处理线程和Handler。

要使用它，首先得创建一个AsyncTask的子类并实现doInBackground()回调方法，这个方法运行在一个后台线程池中。要更新UI的话，还应该实现onPostExecute()方法，这个方法运行在UI线程并且从doInBackground()方法中取回结果，因此可以安全地更新UI。可以在UI线程中调用execute()来执行任务。

下面是前一个例子使用AsyncTask的实现：  

    public void onClick(View v) {
        new DownloadImageTask().execute("http://example.com/image.png");
    }
    
    private class DownloadImageTask extends AsyncTask<String, Void, Bitmap> {
        /** The system calls this to perform work in a worker thread and
          * delivers it the parameters given to AsyncTask.execute() */
        protected Bitmap doInBackground(String... urls) {
            return loadImageFromNetwork(urls[0]);
        }
        
        /** The system calls this to perform work in the UI thread and delivers
          * the result from doInBackground() */
        protected void onPostExecute(Bitmap result) {
            mImageView.setImageBitmap(result);
        }
    }
