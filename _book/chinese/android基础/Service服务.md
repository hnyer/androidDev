# Service 服务
Service 是运行在主线程中的， 如果有耗时操作在Service里，就必须开启一个单独的线程来处理 。

### Service的意义
```text
1、优先级高，不容易被系统杀死。用来实现需要稳定、长时间运行的模块。
(Service的优先级高于后台挂起的Activity， 也高于Activity所创建的Thread)

2、用在一些不适合activity的的场景。例如音乐播放和文件下载等场景 。
```


## Service 启动方式
### start模式
一旦启动，Service将一直运行在后台，即便启动Service的组件已被destroy。
```text
Intent intent = new Intent(this, MyNormalService.class);
startService(intent);//启动服务

stopService(intent) ;//停止服务
//或在服务类中 调用 stopSelf();
```

###  bind模式
通过绑定方式启动的Service是一个client-server结构。
多个组件可与一个service绑定，service不再与任何组件绑定时，该service会被destroy。
```text
Intent intent = new Intent(MainActivity.this, MyNormalService.class) ;
this.bindService( intent, serviceConnection , BIND_AUTO_CREATE  ); // 绑定
this.unbindService(serviceConnection); // 解绑

// 启动远程service ,比如aidl通信时，service不在同一个APP。
Intent intent=new Intent();
intent.setAction("com.remoteService.test"); //用来识别service的标记
intent.setPackage("com.wk.iadlservice"); // 服务所在的应用id
bindService(intent, mConnection, Context.BIND_AUTO_CREATE);

ServiceConnection serviceConnection = new ServiceConnection(){
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        MyNormalService.MyBinder myBinder = (MyNormalService.MyBinder) service ;
        myBinder.func1();//调用自定义Binder中的方法
    }

    @Override
    public void onServiceDisconnected(ComponentName name)   {   }
} ;

//MyNormalService.java
@Override
public IBinder onBind(Intent intent){
    return new MyBinder();
}

class  MyBinder  extends Binder{
   public void func1(){
    ...
   }
}
```
 
```text  
public class MyNormalService extends Service{
    // 通过start方式启动 时回调的方法
    @Override
    public IBinder onBind(Intent intent){  .... }

    // 通过bind方式启动 回调的方法
    @Override
    public int onStartCommand(Intent intent, int flags, int startId){ ... }
}
```

## bindService( , ,) 
bindService(  , ,  int flags ) flags 含义
```text
BIND_AUTO_CREATE    //若绑定服务时服务未启动，则会自动启动服务。
BIND_DEBUG_UNBIND   // 使用此标志绑定服务之后的unBindService 方法会无效。 这种方法会引起内存泄露，建议只在调试时使用。
BIND_NOT_FOREGROUND // 被绑定的服务进程优先级不允许被提到 FOREGROUND 级别
BIND_ABOVE_CLIENT   // 如果当绑定服务期间遇到OOM需要杀死进程，客户进程会先于服务进程被杀死。
BIND_WAIVE_PRIORITY //  被绑定的服务进程不会被OOM列入猎杀对象中。
```

 
## onStartCommand()
默认情况下， onStartCommand 返回值 START_STICKY_COMPATIBILITY 或 START_STICKY 。
```text
1、START_STICKY           //  会重启服务，可能会传递null的intent。
如果Service所在的进程，在执行了onStartCommand方法后，被清理了，
那么这个Service会被保留在已开始的状态，但是不保留传入的Intent，
随后系统会尝试重新创建此Service，由于服务状态保留在已开始状态，
所以创建服务后一定会调用onStartCommand方法。
如果在此期间没有任何启动命令被传递到service，那么参数Intent将为null，需要我们小心处理。

2、START_NOT_STICKY       // 不会重启服务。这是最安全的选项
如果Service所在的进程，在执行了onStartCommand方法后，被清理了，则系统不会重新启动此Service。

3、START_REDELIVER_INTENT //  会重启服务。传入最后一个intent 。
如果系统在 onStartCommand() 返回后终止服务，则会重建服务，并通过 传递给服务的最后一个 Intent。

4、START_STICKY_COMPATIBILITY //   
是START_STICKY 的兼容版本，但是不能保证被清理后 onStartCommand方法一定会被重新调用。
```
 

## 前台服务、后台服务
```text
// 1、前台服务
前台服务必须给状态栏提供一个通知 。 系统内存不足的时候不允许系统杀死的服务。

// 参数一：唯一的通知标识；参数二：通知消息。
xx.startForeground(12345, notification);// 开始前台服务
xx.stopForeground(true);// 停止前台服务--参数：表示是否移除之前的通知
```


## IntentService
```text
1、public abstract class IntentService extends Service..
2、HandlerThread thread = new HandlerThread("IntentService[" + mName + "]")..
3、mServiceHandler = new ServiceHandler(mServiceLooper)..

1、内部已经采用了独立的子线程处理问题。无需手动开启子线程。
2、内部采用队列处理问题。 不用考虑多线程并发问题。
3、任务结束后会自动停止。无需手动调用停止代码。 解决Service内存泄漏问题。
```

 