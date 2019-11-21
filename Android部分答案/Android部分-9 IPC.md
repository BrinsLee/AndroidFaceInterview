### 2.9 IPC

- 1.说说你对Android多进程开发的认识？
> 什么是多进程：
    当一个应用在开始运行时，系统会为它创建一个进程，一个应用默认只有一个进程，这个进程（主进程）的名称就是应用的包名。
> 定义多进程：
     在AndroidManifest.xml中指定android:process属性
     a.android:process = package:remote，将运行在package:remote进程中，属于全局进程，其他具有相同shareUID与签名的APP可以跑在这个进程中。
     b.android:process = :remote ，将运行在默认包名:remote进程中，而且是APP的私有进程，不允许其他APP的组件来访问。
> [点击查看答案](https://www.jianshu.com/p/ce1e35c84134)

- 2.Android中进程间通信的方式有哪些？
> Bundle/Intent传递数据：
      可传递基本类型，String，实现了Serializable或Parcellable接口的数据结构。
      Serializable是Java的序列化方法，Parcellable是Android的序列化方法，前者代码量少（仅一句），但I/O开销较大，一般用于输出到磁盘或网卡；
      后者实现代码多，效率高，一般用户内存间序列化和反序列化传输。
> Messenger：
    基于AIDL实现，服务端（被动方）提供一个Service来处理客户端（主动方）连接，
    维护一个Handler来创建Messenger，在onBind时返回Messenger的binder。         
    双方用Messenger来发送数据，用Handler来处理数据。Messenger处理数据依靠Handler，所以是串行的，也就是说，Handler接到多个message时，就要排队依次处理 
> AIDL：
    AIDL通过定义服务端暴露的接口，以提供给客户端来调用，AIDL使服务器可以并行处理
    通过编写aidl文件来设计想要暴露的接口，编译后会自动生成响应的java文件，服务器将接口的具体实现写在Stub中，用iBinder对象传递给客户端，客户端bindService的时候，用asInterface的形式将iBinder还原成接口，再调用其中的方法。
> ContentProvider：
    系统四大组件之一，自己实现一个ContentProvider需要实现6个方法，其中onCreate是主线程中回调的，其他方法是运行在Binder之中的。
    自定义的ContentProvider注册时要提供authorities属性，应用需要访问的时候将属性包装成Uri.parse("content://authorities")。
    还可以设置permission，readPermission，writePermission来设置权限。 
    ContentProvider有query，delete，insert等方法，看起来貌似是一个数据库管理类，但其实可以用文件，内存数据等等一切来充当数据源，query返回的是一个Cursor，可以自定义继承AbstractCursor的类来实现。
> [点击查看答案](https://www.cnblogs.com/lizhengxian/p/5075635.html)

- 3.什么是AIDL?如何创建一个AIDL。

> 什么是AIDL: AIDL允许定义一个编程的接口来作为客户端和服务端通信的桥梁，AIDL定义了客户端和服务端的编程标准，在Android里边一个进程无法直接访问另一个进程的内存信息，但是要访问的话，我们就需要把他们的设备分解成操作系统能认识的基本数据类型。
  使用AIDL必须要跨进程，所以通讯起来非常耗内存和系统资源，因此，没有必要处处用AIDL，那么什么时候用AIDL呢，通过IPC机制不同的程序访问你的服务，并且服务要处理多线程的时候才有必要使用AIDL，如果你只是想通过IPC机制要不同的程序访问你的服务的时候，实现Binder就可以了；如果只想通过IPC机制，但是不想控制多线程，实现Messanger就可以了，Binder和Messanger都是在单线程中完成的。
> [如何创建一个AIDL](https://www.cnblogs.com/rookiechen/p/5352053.html)