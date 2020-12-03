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

// 获取 Message 的方式
Message message ;
message= new Message();

// Return a new Message instance from the global pool.
// Allows us to avoid allocating new objects in many cases.
message= Message.obtain();
message= Message.obtain( message ) ;
message= Message.obtain( handler ) ;
message= Message.obtain( handler, what) ;
message= Message.obtain( handler ,runnable) ;

// 效果跟  Message.obtain( handler ) 一样 ，因为最终也是执行到了 Message.obtain( handler )
message= handler.obtainMessage();
message= handler.obtainMessage( what);
```

## MessageQueue
消息队列 ,提供入队、出队等功能。系统只会自动给主线程建立 MessageQueue。
(子线程也可以有 MessageQueue，只是需要调用 Looper.prepare();  )
```text
boolean enqueueMessage(Message msg, long when)
Message next()
```

## Handle
负责 Message 的发送和执行处理等。
```text
handler.post(runnable) ;
handler.postAtTime(runnable ,0) ;
handler.postDelayed( runnable ,0) ;
handler.sendMessage(message);
handler.sendEmptyMessage( 1 );
handler.sendMessageDelayed(message, 0);
//  以上 6个函数 都最后会 被执行到 sendMessageAtTime()
handler.sendMessageAtTime(message ,0) ;
// 插入到消息队列最前面
handler.postAtFrontOfQueue(runnable) ;
```

## Looper
循环器，扮演 MessageQueue 和 Handler 之间桥梁的角色，循环取出 MessageQueue 里面的Message，并交付给 Handler 进行处理。

```text
Looper 在prepare中通过ThreadLocal保证了每个线程Looper对象的唯一性， 对于每个线程，有唯一的Looper对象和MessageQueue队列。
loop() 中有一个死循环 for (;;)  ，会不断调用 MessageQueue 的next()，当有消息就处理，否则就阻塞等待。

Looper.loop(); // 死循环 判断是否有需要处理的 Message
public static void loop(){
    final Looper me = myLooper();
    final MessageQueue queue = me.mQueue;
    for (;;)    {
        Message msg = queue.next();       //如果没有消息则阻塞。
        msg.target.dispatchMessage(msg); //将消息进行分发 处理 
    }
}
```

## 一个线程有几个Looper
```text
因为在调用 Looper.prepare() 给当前线程新建 Looper 时有判断 , 所以 只能有一个 Looper
private static void prepare(boolean quitAllowed){
    if (sThreadLocal.get() != null)  {
        throw new RuntimeException("Only one Looper may be created per thread"); 
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```

## 判断是否是主线程
```text
通过判断两个线程的 Looper 是否是同一个。
return  Looper.myLooper() == Looper.getMainLooper();
```


##   HandlerThread
```text
HandlerThread extends Thread..

HandlerThread 是一个内部实现了 Looper循环的线程 。
具有以下特点：
当有耗时任务投放到该循环线程中时，线程执行耗时任务，
执行完之后循环线程处于等待状态，直到下一个新的耗时任务被投放进来。  
减少不停地新建、销毁线程带来的资源消耗。  
```

## ThreadLocal
从ThreadLocal这个名字看，它带着浓浓的“本地线程”的味道；
其实 ThreadLocal并不是用来操作什么本地线程而是用于实现不同线程的数据副本。
每一个线程都可以独立地改变自己的副本并且不会影响其它线程所持有的对应的副本。
```text
在 Looper.java 中 有使用到 ThreadLocal 的这特性。
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
//将新创建的对象的引用保存到各线程的自己的一个map中 
sThreadLocal.set(new Looper(quitAllowed));

public static Looper myLooper() {
// 各线程从自己的map中取出放进去的对象，
// 因此取出来的是各自自己线程中的对象 ,使得每个 thread 都有自己独立的 handle
    return sThreadLocal.get();  
 }
```

## Can't create handler inside thread that has not called Looper.prepare()
```text
在子线程中 直接调用 Handler handler=new Handler() ,会提示这个错误。
因为 在 Handle 的构造函数中 对 Looper 进行检查 

mLooper = Looper.myLooper();
if (mLooper == null) {
    throw new RuntimeException( "Can't create handler inside thread " + Thread.currentThread()  + " that has not called Looper.prepare()");
}

所以需要 手动调用  Looper.prepare()
private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed)); // 通过 ThreadLocal 给当前线程设置 looper
}

而且需要手动调用 Looper.loop() ,开启 handle 对应 looper 的死循环  for (;;)
```

## 为什么主线程中 新建Handle 不要手动调用 Looper.prepare() 和 Looper.loop()
```text
因为在 ActivityThread.java 中的  public static void main(String[] args) 函数中
系统已经 启用了  Looper.prepareMainLooper() 、Looper.loop() 
```

## 主线程中的Looper.loop()一直无限循环为什么不会造成ANR
```text
为什么当主线程处于死循环的 Message msg = queue.next() 这句会阻塞线程的代码的时候不会产生 ANR 异常, 

1、当没有消息的时候会阻塞
2、其他线程有消息发过来，它马上就会被唤醒，去分发message。
所以 阻塞是有的，但是不会报ANR 。
因为ANR的根本原因是在等待的时间内没有回应，
你都没有 Message 要处理，我当然不需要任何回应。 
```

## ANR (Application Not Responding )原理
```text
ANR的监控和处理，是在系统层做的。
系统服务发出一条指令后会在约定的时间等待返回，
如果在约定时间内没有返回，系统就会给出 ANR 提示。
```

## 为什么 Handler 有潜在内存泄露
```text
在 Activity 中 用这种内部类的方式 新建 handle， handle就会持有外部类 activity 的引用，
Handler handler = new Handler() { ... };

同理 ，Runnable 也会拥有 外部类 activity 的引用
handler.postDelayed(new Runnable() {   public void run() { ... }   })
Runnable 被封装成 Message 被 MessageQueue 持有，所以 activity 被 MessageQueue 间接持有了。
activity 退出时，如果延时时间还没到，所以 activity 无法被释放掉。

至于为什么 内部类会持有外部类的引用 ，可以查阅其他博客。
```

## 通过 WeakReference 解决 handle 内存泄漏 
```text
private static class MyHandler1 extends Handler {
    //WeakReference 当GC执行时，无论当前内存是否充足，都会将弱引用关联的对象回收掉。
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


## 为什么 在子线程中调用 activity.runOnUiThread() 也可以更新UI
```text
new Thread(new Runnable() {  activity.runOnUiThread(new Runnable() ... )  }).start();

public final void runOnUiThread(Runnable action) {
    if (Thread.currentThread() != mUiThread) {
        mHandler.post(action);
    } else {
        action.run();
    }
}

从源码可以得知 ，如果调用 runOnUiThread()的线程就是主线程，就立马执行。
如果是在子线程中调用，就会把 Runnable 封装成 Message ，进入消息队列，
最后会转发给 创建 mHandler 所在的线程执行 ，Handler mHandler = new Handler();
mHandler 是系统在 Activity.java 中创建，所以最后会在主线程中执行了。
```
 
## 为什么 在子线程中调用 handler.post(new Runnable...)  也可以更新UI
```text
// 前提条件 ： handler是在 主线程中新建的 
new Thread(new Runnable() {  handler.post(new Runnable() { // 更新 UI  }   ).start();

跟踪源码发现 执行 post 后 ，会经过 一系列函数 ，将 Runnable 包装成 Message ，
最后分发回  handler所依附线程(主线程)中运行。

private static Message getPostMessage(Runnable r) {
    Message m = Message.obtain(); // 包装 Runnable 给 Message
    m.callback = r;
    return m;
}

public void dispatchMessage(@NonNull Message msg) {
    if (msg.callback != null) {
        handleCallback(msg); //  执行 Runnable 的run 函数
    }  
}
    
private static void handleCallback(Message message) {
    message.callback.run();
}
```

## handler 为什么可以更新UI 、为什么可以实现线程间通信
``text
现在有A、B两个线程，在A线程中创建了handler，然后在B线程中调用 A线程的 handler发送一个message。

当A线程创建handler的时候，同时创建了 MessageQueue 与 Looper，
Looper在A线程中被调用，进入 for (;;) 循环 ，轮询 MessageQueue 是否有需要处理的消息。

在B线程使用 A线程中的 handler发送一个message ,将message插入到handler对应的MessageQueue中，
Looper发现有message插入到MessageQueue中，便取出message执行相应的逻辑，

因为Looper.loop() 是在A线程中启动的，对应的 MessageQueue 和 Looper 都是属于 A线程的 ，
所以  Handle 的方法 handleMessage() 是在 A线程中执行。
``

##  Runnable 是一定运行在子线程中吗
```text
Runnable 运行在子线程中是错误的观念。
runnable只是创建了一个执行任务的对象，但是它本身并不会创建一个新的子线程。
最终的执行线程是要看 所依附的线程 。

1、举例 handler.post(runnable)
// The runnable will be run on the thread to which this handler is  attached. 
public final boolean post(@NonNull Runnable r) {
   return  sendMessageDelayed(getPostMessage(r), 0);
}

2、举例view.post(runnable)
The runnable will be run on the user interface thread.
```

## 消息队列如何将消息排序

## Handler.postDelayed()是如何 实现实现延时执行的 ,这种延时方法是一定非常精准吗
```text
// Handler.postDelayed()是如何 实现实现延时执行的。
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

## 如何退出 Looper 循环 、退出 Handle
```text
handler.getLooper().quit(); // 我手动调用这个代码
// Looper.java
public void quit() {
    mQueue.quit(false);
}

// MessageQueue.java 
void quit(boolean safe) {
    synchronized (this) {
        if (mQuitting) {
            return;
        }
        mQuitting = true; // 1、标记可以退出
        if (safe) {
            removeAllFutureMessagesLocked(); // 2、删除相关数据
        } else {
            removeAllMessagesLocked();
        }
        nativeWake(mPtr); // 3、唤醒线程
    }
}

然后再来看 Looper.java 中的 loop() 函数
public static void loop() {
    for (;;) {
        Message msg = queue.next(); // might block ，
        if (msg == null) {
            // 6、nex 返回空 ，退出 loop循环 
            return;
        }
        msg.recycleUnchecked();
    }
}

// MessageQueue.java 
Message next() {
    for (;;) {
        // nativePollOnce 用于“等待”, 直到下一条消息可用为止
        // 4、被step3唤醒 ，继续往下执行
        nativePollOnce(ptr, nextPollTimeoutMillis);
        synchronized (this) {
            if (mQuitting) { // 5、mQuitting 在 step 1被设置为 true了 ，所以返回 null
                dispose();
                return null;
            }
    }
``` 

##  主线程 的 looper 能否 quit()
```text
void quit(boolean safe) {
    if (!mQuitAllowed) {
        throw new IllegalStateException("Main thread not allowed to quit.");
    }
   }
从源码可知，主线程的Looper 禁止开发者 quit 。
因为主线程中的 handle 要处理很多事情，退出之后 整个APP就没得玩了。

private class H extends Handler｛
    public void handleMessage(Message msg) {
    switch (msg.what) {
        case LAUNCH_ACTIVITY 
        case RELAUNCH_ACTIVITY: {
        case PAUSE_ACTIVITY: {
        case PAUSE_ACTIVITY_FINISHING: 
        case STOP_ACTIVITY_SHOW: 
        case STOP_ACTIVITY_HIDE:  
        case LOCAL_VOICE_INTERACTION_STARTED:
        ...
```

## Handler 如何确保线程安全
```text
// 与 handle 对应的 MessageQueue ，Message 进入队列 synchronized 上锁了
boolean enqueueMessage(Message msg, long when) { // MessageQueue.java 
    synchronized (this) {
            
// Looper 中获取 Message 时 MessageQueue 的next 也被上锁了
for (;;) { // Looper.java 
  Message msg = queue.next(); 

Message next() { // MessageQueue.java  
 synchronized (this) { ...  }   }
```

## 手写 handle 机制 实现 线程间通信
handle 不仅仅只用于 主线程 和子线程的通信 ，也可以实现 子线程和子线程的通信 。
可以参考我的 demo  https://gitee.com/hnyer/my-handle 
 