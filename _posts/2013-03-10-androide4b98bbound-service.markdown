---
author: gavin
comments: true
date: 2013-03-10 00:35:09
layout: post
slug: android%e4%b9%8bbound-service
title: Android之Bound Service
wordpress_id: 104
categories:
- Android
tags:
- AIDL
- Android
- Binder
- Bound Service
- IPC
- Messenger
- Service
---

### 
	Bound Service简介：






	Bound Service相当于C/S架构中的Server。它允许其它组件（客户端）绑定到它，向它发送请求并接受应答，甚至执行IPC。Bound Service适用于需要向其它组件提供服务，并且不会在后台无限期运行的场合。






	要实现一个Bound Service，必须重写onBind()回调方法。这个方法返回一个定义了客户端与服务端编程接口的IBinder对象。客户端可以调用bindService()绑定到Service，此时客户端必须提供一个ServiceConnection的实现，这个实现可以监控与Service的连接。bindService()方法会立即返回（返回类型为void），当系统在客户端与Service之间创建一个连接后，ServiceConnection的onServiceConnected()方法就会被调用，此时客户端就可以得到IBinder对象并用它与Service交互。






	多个客户端可以同时绑定到一个Service，但是，系统只会在第一个客户端绑定到时调用onBind()方法，后来绑定到的客户端都会被返回与第一个绑定到的客户端相同的IBinder。






	当最后一个客户端从Service解绑后，系统就会销毁这个Service（除非Service同时是被startService()启动的）。





### 
	创建一个Bound Service：






	定义IBinder接口，可以有三种方式：






	1.继承Binder类：当Service相对于自己的应用是私有的，并且与客户端运行在同一个进程，就应该通过onBind()返回一个继承自Binder类的实例。客户端接收到这个Binder后，可以用它直接访问这个Binder实现甚至Service的公共方法。






	如果Service只是在后台为自己的应用工作，这是最好的技术。






	2.使用Messenger：如果接口需要在不同进程间工作，可以为Service提供一个Messenger接口。在这种方式中，Service使用一个Handler应答不同类型的Message对象。这个Handler是Messenger的基础，它与客户端共享一个IBinder，并且允许客户端使用Message对象向Service发送命令。另外，客户端可以定义一个自己的Messenger，这样Service就可以使用这个Messenger向它返回结果。






	这是执行IPC的最简单的方式，由于Messenger将所有请求顺序排入了一个单线程，所以Service不用设计成线程安全的。






	3.使用AIDL:AIDL（Android Interface Definition Language，Android接口描述语言）将所有的工作对象分解为操作系统可认的基本数据类型，并且安排它们跨进程以执行IPC。Messenger实际上正是基于AIDL底层结构的技术。如上文所说，Messenger在一个单线程中创建一个包含所有客户端请求的队列，因此Service只能同时接收一个请求。所以，如果希望Service可以同时处理多个请求，就应该直接使用AIDL。此时，Service必须能在多线程下工作，并且是线程安全的。






	注意：大多数应用不应该使用AIDL去创建一个Bound Service，因为它可能需要多线程能力并导致更复杂的实现。因此，AIDL是不适用于大多数应用的。  

******继承Binder类：** 






	如果Service只被用于本地应用并且不需要跨进程，可以给客户端提供一个自己的Binder实现，客户端可以通过这个Binder直接访问Service的公共方法。  

注意：这种方式只适合于客户端和Service处于相同的应用和进程的情况下。






	下面是建立这种方式的步骤：






	1.在Service中，创建一个下面三种方式中任意一个类型的Binder实例：  

①包含客户端可以调用的公共方法。  

②返回当前的Service实例，这个Service拥有客户端可以调用的公共方法。  

③或者，返回一个Service中另外一个类的实例，客户端可以通过这个实例调用Service中的公共方法。






	2.从onBind()回调方法中返回这个Binder实例。






	3.在客户端中，在onServiceConnected()回调方法中接收Binder，并且使用这个Binder调用Bound Service中的方法。






	注意：Service和客户端必须要在同一个应用中的理由是客户端可以转换返回的对象并且正确地调用它的API。Service和客户端必须在同一个进程，是因为这种技术不能执行任何跨进程的任务。






	下面是Android文档提供的一个例子：






	 




    
    public class LocalService extends Service {
        // Binder given to clients
        private final IBinder mBinder = new LocalBinder();
        // Random number generator
        private final Random mGenerator = new Random();
    
        /**
         * Class used for the client Binder.  Because we know this service always
         * runs in the same process as its clients, we don't need to deal with IPC.
         */
        public class LocalBinder extends Binder {
            LocalService getService() {
                // Return this instance of LocalService so clients can call public methods
                return LocalService.this;
            }
        }
    
        @Override
        public IBinder onBind(Intent intent) {
            return mBinder;
        }
    
        /** method for clients */
        public int getRandomNumber() {
          return mGenerator.nextInt(100);
        }
    }





	LocalBinder为客户端提供了getService()方法来获得LocalService的实例。这种方式允许客户端调用Service的公共方法。例如，客户端可以调用LocalService的getRandomNumber()方法。






	下面是一个绑定到LocalService的Activity，当一个button被点击时就会调用getRandomNumber()。




    
    public class BindingActivity extends Activity {
        LocalService mService;
        boolean mBound = false;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
        }
    
        @Override
        protected void onStart() {
            super.onStart();
            // Bind to LocalService
            Intent intent = new Intent(this, LocalService.class);
            bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            // Unbind from the service
            if (mBound) {
                unbindService(mConnection);
                mBound = false;
            }
        }
    
        /** Called when a button is clicked (the button in the layout file attaches to
          * this method with the android:onClick attribute) */
        public void onButtonClick(View v) {
            if (mBound) {
                // Call a method from the LocalService.
                // However, if this call were something that might hang, then this request should
                // occur in a separate thread to avoid slowing down the activity performance.
                int num = mService.getRandomNumber();
                Toast.makeText(this, "number: " + num, Toast.LENGTH_SHORT).show();
            }
        }
    
        /** Defines callbacks for service binding, passed to bindService() */
        private ServiceConnection mConnection = new ServiceConnection() {
    
            @Override
            public void onServiceConnected(ComponentName className,
                    IBinder service) {
                // We've bound to LocalService, cast the IBinder and get LocalService instance
                LocalBinder binder = (LocalBinder) service;
                mService = binder.getService();
                mBound = true;
            }
    
            @Override
            public void onServiceDisconnected(ComponentName arg0) {
                mBound = false;
            }
        };
    }





	******使用Messenger：****** 






	如果需要Service与远程进程交互，就可以使用一个Messenger暴露这个Service的接口。这种技术允许不通过AIDL执行IPC。






	下面是关于如何使用Messenger的简介：






	1.Service提供一个Handler实现，这个Handler接收来自一个客户端的每一个调用的回调。






	2.用Handler来创建一个Messenger对象，这个对象拥有前面Handler的一个引用。






	3.这个Messenger对象创建一个IBinder，随后Service从onBind()向客户端返回这个IBinder对象。






	4.客户端使用这个IBinder初始化拥有Service的Handler引用的Messenger，客户端可以使用这个Messenger对象向Service发送Message对象。






	5.Service在Handler的handleMessage()方法中接收每一个Message。






	在这种情况下，客户端不会去调用Service中的方法，取而代之的是，客户端给Service的Handler提供消息。  






    
    public class MessengerService extends Service {
        /** Command to the service to display a message */
        static final int MSG_SAY_HELLO = 1;
    
        /**
         * Handler of incoming messages from clients.
         */
        class IncomingHandler extends Handler {
            @Override
            public void handleMessage(Message msg) {
                switch (msg.what) {
                    case MSG_SAY_HELLO:
                        Toast.makeText(getApplicationContext(), "hello!", Toast.LENGTH_SHORT).show();
                        break;
                    default:
                        super.handleMessage(msg);
                }
            }
        }
    
        /**
         * Target we publish for clients to send messages to IncomingHandler.
         */
        final Messenger mMessenger = new Messenger(new IncomingHandler());
    
        /**
         * When binding to the service, we return an interface to our messenger
         * for sending messages to the service.
         */
        @Override
        public IBinder onBind(Intent intent) {
            Toast.makeText(getApplicationContext(), "binding", Toast.LENGTH_SHORT).show();
            return mMessenger.getBinder();
        }
    }





	此时，客户端需要做的就是基于Service返回的IBinder创建一个Messenger对象，并且使用send()发送一条消息。






	 




    
    public class ActivityMessenger extends Activity {
        /** Messenger for communicating with the service. */
        Messenger mService = null;
    
        /** Flag indicating whether we have called bind on the service. */
        boolean mBound;
    
        /**
         * Class for interacting with the main interface of the service.
         */
        private ServiceConnection mConnection = new ServiceConnection() {
            public void onServiceConnected(ComponentName className, IBinder service) {
                // This is called when the connection with the service has been
                // established, giving us the object we can use to
                // interact with the service.  We are communicating with the
                // service using a Messenger, so here we get a client-side
                // representation of that from the raw IBinder object.
                mService = new Messenger(service);
                mBound = true;
            }
    
            public void onServiceDisconnected(ComponentName className) {
                // This is called when the connection with the service has been
                // unexpectedly disconnected -- that is, its process crashed.
                mService = null;
                mBound = false;
            }
        };
    
        public void sayHello(View v) {
            if (!mBound) return;
            // Create and send a message to the service, using a supported 'what' value
            Message msg = Message.obtain(null, MessengerService.MSG_SAY_HELLO, 0, 0);
            try {
                mService.send(msg);
            } catch (RemoteException e) {
                e.printStackTrace();
            }
        }
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.main);
        }
    
        @Override
        protected void onStart() {
            super.onStart();
            // Bind to the service
            bindService(new Intent(this, MessengerService.class), mConnection,
                Context.BIND_AUTO_CREATE);
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            // Unbind from the service
            if (mBound) {
                unbindService(mConnection);
                mBound = false;
            }
        }
    }





	如果希望得到Service的回应，就需要在客户端中创建一个Messenger。然后在客户端接收到onServiceConnected()回调的时候，使用send()发送一个包含了客户端Messenger对象（将Message对象的replyTo参数设为此Messenger对象）的Message给Service。





### 
	另外需要注意的一些地方：






	1.应该始终捕获DeadObjectException异常，当连接被破坏的时候会抛出这个异常。它也是远程方法唯一可以抛出的异常。






	2.对象在整个过程都持有引用记数。






	3.通常应该在客户端生命周期中匹配的产生和拆除方法中，各自配对地绑定和取消绑定服务。例如，如果只需要在Activity显示时与Service交互，就应该在onStart()方法中绑定，并在onStop()方法中取消绑定。  







### 
	Bound Service的生命周期：






	当所有客户端都取消与Service的绑定后，Android系统就会销毁它（除非它同时也是通过onStartCommand()启动的）。因此，如果是一个纯粹的Bound Service，就不应该管理这个Service的生命周期。






	如果这个Service既是Bound Service又是Started Service，则这个Service的销毁取决于所有客户端是否都取消绑定，并且是否调用过stopSelf()或由其它组件调用stopService()。






	另外，如果Service是Started Service并接受绑定，当系统调用onUnbind()方法并返回true时，如果有一个客户端调用了bindService()，那么Service就会在onRebind()中接收调用而不是在onBind()中。onRebind()返回void，但是客户端仍然可以在它的onServiceConnected()中接收IBinder。  

![](http://droider-wordpress.stor.sinaapp.com/service_binding_tree_lifecycle.png) 






	  







	  


