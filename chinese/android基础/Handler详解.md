# Handle、Looper 、Message、MessageQueue 模型
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802281118_osChina_Handler模型.png)

简单的说：一个线程开启一个无限循环模式，不断遍历自己的消息列表，如果有消息就挨个拿出来做处理，如果列表没消息，自己就堵塞（相当于wait，让出cpu资源给其他线程），其他线程如果想让该线程做什么事，就往该线程的消息队列插入消息，该线程会不断从队列里拿出消息做处理。 

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
消息队列 ,提供入队、出队等功能。系统只会自动给主线程建立 MessageQueue。(其他子线程也可以有 MessageQueue，只是需要调用 Looper.prepare();//创建该子线程的Looper )
```text
boolean enqueueMessage(Message msg, long when)
Message next()
```

## Handle
负责 Message 的发送和执行处理等。
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

// 使用场景如下： 在子线程中调用post()  ，handler是在主线程中创建的
handler.post(new Runnable(){
    @Override
    public void run()  {
       //为什么此处可以更新UI？  
       // 因为跟踪源码会最终发现 ，这个runnable会在这个handler所依附线程中运行，而这个handler是在UI线程中创建的，所以  自然地依附在主线程中了。
    }
});

// handler 为什么可以更新UI 、为什么可以实现线程切换
例如现在有A、B两个线程，在A线程中有创建了handler，然后在B线程中调用handler发送一个message。
当在A线程中创建handler的时候，同时创建了 MessageQueue与Looper，Looper在A线程中调用loop进入一个无限的for循环从MessageQueue中取消息，
当B线程调用handler发送一个message的时候，会通过msg.target.dispatchMessage(msg);将message插入到handler对应的MessageQueue中，
Looper发现有message插入到MessageQueue中，便取出message执行相应的逻辑，
因为Looper.loop()是在A线程中启动的，所以则回到了A线程，达到了从B线程切换到A线程的目的。

// Handler.postDelayed()是如何 实现实现延时执行的。这种延时方法是一定非常精准吗？
答：会马上进入队列的，而不是等时间到了再加入队列。大概流程如下：
1、比如postDelay 一个延时10秒钟的A消息进队，MessageQueue调用 nativePollOnce ()阻塞，Looper阻塞；
2、紧接着post 一个B消息进队，判断现在A时间还没到、正在阻塞，把B插入消息队列的头部（A的前面），然后调用nativeWake()方法唤醒线程；
3、MessageQueue.next()方法被唤醒后，重新开始读取消息链表，第一个消息B无延时，直接返回给Looper；
4、Looper处理完这个消息再次调用next()方法，MessageQueue继续读取消息链表，第二个消息A还没到时间，
计算一下剩余时间（假如还剩9秒）继续调用nativePollOnce()阻塞；直到阻塞时间到或者下一次有Message进队再次唤醒；

这种延时方法不准确，如果上一个runable耗时操作的话，就很不准确了。
handler.post( new Runnable() {
    public void run() {
        SystemClock.sleep(1000);
    }
});
final  long startime = System.currentTimeMillis() ;
handler.postDelayed(new Runnable() {
    public void run() {
        Log.i("handlerTag" , "时间差="+ (System.currentTimeMillis()- startime)) ;
        // 时间差=1045 附近，反正不是500
    }
} , 500) ;
```

## Looper
循环器，扮演 MessageQueue 和 Handler 之间桥梁的角色，循环取出 MessageQueue 里面的Message，并交付给 Handler 进行处理。

```text
Looper 在prepare中通过ThreadLocal保证了每个线程Looper对象的唯一性， 对于每个线程，有唯一的Looper对象和MessageQueue队列。
oop()是一个死循环，会不断调用 MessageQueue 的next()，当有消息就处理，否则就阻塞等待。

// ThreadLocal.java 
private static void prepare(boolean quitAllowed){
   sThreadLocal.set(new Looper(quitAllowed)); // 将新创建的对象的引用保存到各线程的自己的一个map中.
}
public static @Nullable Looper myLooper(){
  return sThreadLocal.get();//执行ThreadLocal.get()时，各线程从自己的map中取出放进去的对象， 因此取出来的是各自自己线程中的对象
}

// 主线程中的Looper.loop()一直无限循环为什么不会造成ANR？
为什么当主线程处于死循环的 Message msg = queue.next() 这句会阻塞线程的代码的时候不会产生 ANR 异常, 
先说结论：阻塞是有的，但是不会卡住 
1、当没有消息的时候会阻塞
2、其他线程有消息发过来，它马上就会被唤醒，去分发message。

// ANR 原理
Application Not Responding ，ANR的监控和处理，是在系统层做的。系统服务(例如ActivityManagerService,AMS) 发出一条指令后会在约定的时间等待返回，
如果在时间内返回，系统就会给出弹窗提示。

//线程中默认没有 Looper，调用 Looper.prepare() 方法为当前线程创建一个 Looper
Looper.prepare();
private static void prepare(boolean quitAllowed){
    if (sThreadLocal.get() != null)  {
        // Android如何保证一个线程最多只能有一个Looper？
        throw new RuntimeException("Only one Looper may be created per thread"); //确保一个线程中只有一个Looper对象
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
         msg.target.dispatchMessage(msg); //将消息进行分发 处理 
    }
}
```


# 判断是否是主线程
```text
public static boolean isMainThread(){
   return  Looper.myLooper() == Looper.getMainLooper();
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


#   HandlerThread
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

# runOnUiThread
```text
在子线程中 使用 runOnUiThread 为什么可以更新UI ?
findViewById(R.id.testBtn).setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        new Thread(new Runnable() {
            public void run() {
                activity.runOnUiThread(new Runnable() {
                    public void run() {
                        infoTv.setText("work thread");
                    }
                });
            }
        }).start();
    }
});

// 首先说一个容易忽略的事实， 子线程不允许更改ui 只是安卓定下的一个机制。
从实现上来讲子线程是可以更改布局界面的，只是在代码只做了限制，更新ui的时候会判断线程是不是主线程，如果不是就报异常，这是一种机制。
如果 直接在 onCreate 中新建一个线程 更改ui ，然后不做其他事情马上退出，还来不及检查是否在主线程，线程就退出了，此时UI改变了，但是并不会报错。

至于为什么说 在子线程中 调用 runOnUiThread 就可以更新UI，原理如下 ，
public final void runOnUiThread(Runnable action) {
    if (Thread.currentThread() != mUiThread) {
        mHandler.post(action); // 如果是子线程中 调用 runOnUiThread ， 就将 runnable 加入到 activity所以在线程中(即主线程)的的消息队列中，然后通过
                                // Looper 轮询时取出，交给 处于主线程中的 handler进行处理， handler是在主线程中创建的，所以就在主线程中可以更新UI了。
    } else {
        action.run(); // 如果是在主线程中调用 runOnUiThread ， 就立即执行，不需要用handler进行转发。
    }
}

```