# Binder与安卓Service


#### Service的意义
1、优先级高，不容易被系统杀死。用来实现需要稳定、长时间运行的模块。(Service的优先级高于后台挂起的Activity， 也高于Activity所创建的Thread)

2、用在一些不适合activity的的场景。例如音乐播放和文件下载等场景 。

#### 注意
Service 是运行在主线程中的， 如果有耗时操作在Service里，就必须开启一个单独的线程来处理 。




#### Service有两种启动形式：

1、start模式。
一旦启动，Service将一直运行在后台，即便启动Service的组件已被destroy。

```java
//启动服务
Intent intent = new Intent(this, MyNormalService.class);
startService(intent);

//停止服务
stopService(intent) ;
//或在服务类中 调用 stopSelf();
```

2、bind模式。
通过绑定方式启动的Service是一个client-server结构。多个组件可与一个service绑定，service不再与任何组件绑定时，该service会被destroy。
```java
//MainActivity.java
Intent intent = new Intent(MainActivity.this, MyNormalService.class) ;
this.bindService( intent, serviceConnection , BIND_AUTO_CREATE  );
this.unbindService(serviceConnection);

ServiceConnection serviceConnection = new ServiceConnection()
{
    @Override
    public void onServiceConnected(ComponentName name, IBinder service)
    {

        MyNormalService.MyBinder myBinder = (MyNormalService.MyBinder) service ;
        //调用自定义Binder中的方法
        myBinder.func1();
    }

    @Override
    public void onServiceDisconnected(ComponentName name)
    {

    }
} ;

//MyNormalService.java
@Override
public IBinder onBind(Intent intent)
{
    return new MyBinder();
}

class  MyBinder  extends Binder
{
   public void func1()
   {
    ...
   }
}
```

//bindService( , ,)第三个参数

flags | 值  | 含义
---|---
BIND_AUTO_CREATE|  1  | ?
BIND_DEBUG_UNBIND|  2  | ?
BIND_NOT_FOREGROUND|  4  | ?
BIND_ABOVE_CLIENT| 8 | ?
BIND_WAIVE_PRIORITY|  32| ?
-|0| 若不打算指定模式，可传入0


3、bindService + startService 两种模式混合使用 。
暂时还没用过。



#### 前台服务、后台服务
1、前台服务

前台服务必须给状态栏提供一个通知 。 系统内存不足的时候不允许系统杀死的服务。

```java
// 参数一：唯一的通知标识；参数二：通知消息。
startForeground(12345, notification);// 开始前台服务
stopForeground(true);// 停止前台服务--参数：表示是否移除之前的通知
```

&nbsp;&nbsp;
&nbsp;&nbsp;

```java  
public class MyNormalService extends Service
{
    @Override
    public void onCreate()
    {
        super.onCreate();
    }

    /**
     * 通过start方式启动 时回调的方法
     */
    @Override
    public IBinder onBind(Intent intent)
    {
        return null;
    }

    /**
     * 通过bind方式启动 回调的方法
     */
    @Override
    public int onStartCommand(Intent intent, int flags, int startId)
    {

        //return super.onStartCommand(intent, flags, startId);
        //START_STICKY、START_REDELIVER_INTENT
        return  START_NOT_STICKY ;
    }


    @Override
    public void onDestroy()
    {
        super.onDestroy();
    }

}
```


```java  
public class MyIntentService extends IntentService
{

    public MyIntentService(String name)
    {
        super(name);
    }

    @Override
    protected void onHandleIntent(@Nullable Intent intent)
    {
        //耗时操作
    }

}

```


&nbsp;&nbsp;
&nbsp;&nbsp;


#### 同一个进程中Activity与service通信方案
1、借助 EventBus 等第三方框架 。

2、 binder + 回调(listener)。



#### 不同进程间Activity与service通信
？？？


#### 进程重启、进程保活
？？？


#### IntentService

```java

1、public abstract class IntentService extends Service..
2、HandlerThread thread = new HandlerThread("IntentService[" + mName + "]")..
3、mServiceHandler = new ServiceHandler(mServiceLooper)..

```

1、内部已经采用了独立的子线程处理问题。  无需手动开启子线程。
2、内部采用队列处理问题。 不用考虑多线程并发问题。
3、任务结束后会自动停止。无需手动调用停止代码。 解决Service内存泄漏问题。



&nbsp;&nbsp;
&nbsp;&nbsp;
#### PendingIntent




&nbsp;&nbsp;
&nbsp;&nbsp;
#### onStartCommand()的返回值
命令|含义
---|---|---
START_NOT_STICKY| 若执行完onStartCommand()方法后，系统就kill了service，不要再重新创建service，除非系统回传了一个pending intent。
START_STICKY|若系统在onStartCommand()执行并返回后kill了service，那么service会被recreate并回调onStartCommand()
START_REDELIVER_INTENT|若系统在onStartCommand()执行并返回后kill了service，那么service会被recreate并回调onStartCommand()并将最后一个Intent回传至该方法。
