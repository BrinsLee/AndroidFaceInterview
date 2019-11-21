### 2.4 Service

- 1.什么是Service?
> Service(服务)是一个一种可以在后台执行长时间运行操作而没有用户界面的组件。它运行于UI线程，因此不能进行耗时的操作。

- 2.说说Service的生命周期。
> startService() 
> onCreate() -> onStartCommand() -> 调用stopService()/stopSelf() -> onDestroy()

> bindService()
> onCreate() -> onBind() -> 所有client都调用了unbindService() -> onUnbind() -> onDestroy()
- 3.Service和Thread的区别？

> Service的运行是在UI线程当中的，是绝对绝对不能进行耗时操作的，而Thread开启的子线程则可以进行耗时操作，
> 但是Thread开启的子线程是不能直接对UI进行操作的，否则极有可能发生直接让程序崩掉，这就是它们的区别。
> [1~3答案](https://blog.csdn.net/clandellen/article/details/79276411)

- 4.Android 5.0以上的隐式启动问题及其解决方案。

> [点击查看答案](https://www.cnblogs.com/momoshengxiao/p/6442380.html)

- 5.给我说说Service保活方案。

> onStartCommand方法，返回START_STICKY
> 提升service进程优先级: 1) 前台进程 2） 可见进程 3）服务进程 4）后台进程 5）空进程
> onDestroy方法里重启service
  service + broadcast 方式，就是当service走onDestroy的时候，发送一个自定义的广播，当收到广播的时候，重新启动service；
> Application加上Persistent属性

> [点击查看答案](https://www.cnblogs.com/blosaa/p/9530625.html)

- 6.IntentService是什么 & 原理 & 使用场景。
> 是什么
    它的优先级高于Service。
      IntentService是继承处理异步请求的一个类，在IntentService内有一个工作线程来处理耗时操作，
        启动IntentServiced的方式和启动传统的Service一样，同时，当任务执行完成后，
        IntentService会自动停止，而不需要我们手动去控制或stopSelf()。
        另外，可以启动IntentService多次，
        而每一个耗时操作会以工作队列的方式在IntentService的onHandlerIntent回调方法中执行，
        并且，每次只执行一个工作线程，执行完第一个再执行第二个。
    
      a.它本质是一种特殊的Service,继承自Service并且本身就是一个抽象类。
    
      b.它内部是由HandlerThread和Handler实现异步操作。

> [点击查看答案](https://blog.csdn.net/ClAndEllen/article/details/79346624)

- 7.创建一个独立进程的Service应该怎样做？

> [点击查看答案](https://www.jianshu.com/p/4a83becd758e)

- 8.Service和Activity之间如何通信？

> [点击查看答案](http://www.cnblogs.com/codingblock/p/4850299.html)

- 9.说说你了解的系统Service。

> [点击查看答案](https://blog.csdn.net/geyunfei_/article/details/78851024)

- 10.谈谈你对ActivityManagerService的理解。

> [点击查看答案](https://www.cnblogs.com/xingchenkn/p/3637137.html)

- 11.在Activtiy中创建一个Thread和在一个Service中创建一个Thread的区别？

> 这题实际是在考察前台和后台线程,Activity和Service的区别。毫无疑问Activtiy中的线程是前台线程，它的生命周期往往是随着Activity的,Activity销毁的时候，那个线程也应该被销毁，否则就会出现内存泄漏现象。而Service中开启的线程，它是工作在后台的，一般来讲，后台线程的生存期是比较长的。