# Handle、Looper 、Message、MessageQueue

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802281118_osChina_Handler模型.png)

##  Message
定义了消息必要的描述和属性数据。
```text
public final class Message implements Parcelable{
    public int what;//用来标识一个消息.区分来源
    public int arg1; //简单消息
    public int arg2;
    public Object obj; //任意类型数据
    public Messenger replyTo;
    Bundle data;//存储复杂点的对象
    Handler target;//发送和处理消息关联的 Handler
    Runnable callback; //消息的回调
    ......
}


//不推荐
Message msg = new Message();
msg.what =100;

//推荐
msg= Message.obtain();
msg= Message.obtain(Handler h, int what)//有多个方法重载

//推荐
msg= handler.obtainMessage();
msg= handler.obtainMessage(int what);
```

## MessageQueue
消息队列 ,提供入队、出队等功能 。系统只会自动给主线程建立MessageQueue。
```text
boolean enqueueMessage(Message msg, long when)
Message next()
```

## Handle
负责Message 的发送和执行处理等。
```text
handler.post(Runnable r) ;
handler.postAtFrontOfQueue(Runnable r);
handler.postDelayed(Runnable r, long delayMillis);

handler.sendEmptyMessage(int what);
handler.sendMessage(Message msg); //往队列里添加一条信息
handler.sendMessageDelayed(Message msg, long delayMillis);

//接收Msg (重写当前方法，处理接收的值)
handler.handleMessage(Message msg);

//分发消息，可以做拦截操作
handler.dispatchMessage(Message msg);
public void dispatchMessage(Message msg){
     //Runnable getCallback()
    if (msg.callback != null)  {
        handleCallback(msg);
    } else {
        if (mCallback != null)  {
            if (mCallback.handleMessage(msg))   {
                return;
            }
        }
        handleMessage(msg);
    }
}

//在子线程中调用post()
//handler是在主线程中创建的
handler.post(new Runnable(){
    @Override
    public void run()  {
        //为什么此处可以更新UI？
        //因为跟踪源码发现，最后会跟sendMessage()一样调用 enqueueMessage()从而进入队列
    }
});
```

## Looper
主线程中的Looper生命周期和当前应用一样长。
一个MessageQueue 对应一个Looper。
```text
//线程中默认没有 Looper，调用 Looper.prepare() 方法为当前线程创建一个 Looper
Looper.prepare();
private static void prepare(boolean quitAllowed){
    if (sThreadLocal.get() != null)  {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}

//调用 loop() 方法调度消息。
Looper.loop();
public static void loop(){
    final Looper me = myLooper();
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;

    for (;;)    {
        Message msg = queue.next();   //取出一条消息，如果没有消息则阻塞。
        //...
        try {
            msg.target.dispatchMessage(msg); //将消息进行分发 处理
        } 
        //...
    }
}
```


## 当前线程如何和Looper关联的
```text
public class ThreadLocal<T> {... }
 
private static void prepare(boolean quitAllowed){
   ....
   //ThreadLocal.set()将新创建的对象的引用保存到各线程的自己的一个map中.
   sThreadLocal.set(new Looper(quitAllowed));
}

public static @Nullable Looper myLooper(){
  //执行ThreadLocal.get()时，各线程从自己的map中取出放进去的对象，
  //因此取出来的是各自自己线程中的对象
  return sThreadLocal.get();
}
```


##  判断是否是主线程
```text
public static boolean isMainThread(){
  boolean resul = Looper.myLooper() == Looper.getMainLooper();
   return resul ;
}//


public static @Nullable Looper myLooper(){
    return sThreadLocal.get();
}

public static Looper getMainLooper(){
    synchronized (Looper.class){
        return sMainLooper;
    }
}
```



##  Android如何保证一个线程最多只能有一个Looper？
```text
private static void prepare(boolean quitAllowed){
    if (sThreadLocal.get() != null){
      //确保一个线程中只有一个Looper对象
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```



## 主线程中的Looper.loop()一直无限循环为什么不会造成ANR？
ActivityThread.java (被打了@hide标记) 的main方法，是整个应用进程的入口。
只是传递、分发消息而已，不会耗时多少，当然不会ANR.

```text
public static void main(String[] args){
     ...
     //为主线程创建了 Looper
     Looper.prepareMainLooper();

     if (false)  {
         Looper.myLooper().setMessageLogging(new LogPrinter(Log.DEBUG, "ActivityThread"));
     }
     //进入消息循环  loop()里面有死循环。  for (;;)
     Looper.loop();
     ...
 }


 public void handleMessage(Message msg) {
    switch (msg.what){
        case LAUNCH_ACTIVITY: break;
        case RELAUNCH_ACTIVITY:   break;
        case PAUSE_ACTIVITY: break;
         ...
    }
  }
```


##  子线程与子线程通信、 主线程往子线程发送信息
1、全局变量方式。

2、使用 EventBus 等第三方框架 的总线方式 。

3、使用 Android handler机制 。



## HandlerThread

```text
HandlerThread extends Thread..

HandlerThread 是一个内部实现了 Looper循环的线程 。
具有以下特点：
当有耗时任务投放到该循环线程中时，线程执行耗时任务，
执行完之后循环线程处于等待状态，直到下一个新的耗时任务被投放进来。  
减少不停地新建、销毁线程带来的资源消耗。  
```


## 自定义Handle详解
```text
private static class MyHandler1 extends Handler {
    //WeakReference 当GC执行时，无论当前内存是否充足，都会将若引用关联的对象回收掉。
    private final WeakReference<InfoHudViewHolder> weakReference;

    public MyHandler1(InfoHudViewHolder controller) {
        weakReference = new WeakReference<>(controller );
    }

    @Override
    public void handleMessage(Message msg) {
        InfoHudViewHolder holder = weakReference.get() ;
        if(holder==null){
            return;
        }

    }
}
// 使用
MyHandler  myHandler =new MyHandler(this) ;
```
