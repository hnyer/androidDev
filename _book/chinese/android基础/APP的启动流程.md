# APP的启动流程
https://www.jianshu.com/p/a72c5ccbd150

专业名词|作用
-|-
zygote进程|在系统发出请求时，负责分裂出其它的进程。
SystemServer进程|android系统里面重要的服务都是在这个进程里面开启的
ActivityManagerService(AMS)|负责系统中所有Activity的生命周期。
Instrumentation|完成对Application和Activity初始化和生命周期的工具类


## 如何判断一个 APP 在前台还是后台
借用系统 ActivityLifecycleCallbacks 的回调来进行判断。
进入后台后，有一些机型貌似弹不出toast，比如华为M3 。
暂时没找到解决办法，我试过一些银行的APP 和所得税APP，同样也弹不出来。
```text

/**
 * 原理：程序中任何一个activity处于后台时(onStope)都将触发提醒。
 * 所以，如果顶层activity中onStope方法被执行，则认为程序处于后台。
 */
public class MyAppLifecycleHandler implements Application.ActivityLifecycleCallbacks {
    private int started = 0;
    private int stopped = 0;
    private String lastActivitName ;
    private boolean flag = true;

    public MyAppLifecycleHandler(String name){
        lastActivitName= name ;
    }

    @Override
    public void onActivityCreated(Activity activity, Bundle savedInstanceState) {  }

    @Override
    public void onActivitySaveInstanceState(Activity activity, Bundle outState) {  }

    @Override
    public void onActivityResumed(Activity activity) {  }

    @Override
    public void onActivityPaused(Activity activity) {  }

    @Override
    public void onActivityStarted(Activity activity) {
        ++started;
    }

    @Override
    public void onActivityStopped(final Activity activity) {
        ++stopped;
        if (!isApplicationInBackground()){
            return;
        }

        flag = true;
        new Handler().postDelayed(new Runnable() {
            @Override
            public void run() {
                if ( flag ) {
                    Toast.makeText(activity, "WkPlayer被切换至后台", Toast.LENGTH_SHORT).show();
                }
            }
            // 延时的作用是用来等待一下，看 onActivityDestroyed 是否被执行了。
        } ,500) ;
    }

    @Override
    public void onActivityDestroyed(Activity activity) {
        if (lastActivitName.contains(activity.getClass().getSimpleName())) {
            flag = false;
        }
    }

    private boolean isApplicationInBackground() {
        return started == stopped;
    }
}
```


```text
// Application 中调用
MyAppLifecycleHandler lifecycleHandler = new MyAppLifecycleHandler( FileExplorerActivity.class.getSimpleName() ) ;
unregisterActivityLifecycleCallbacks(lifecycleHandler);
registerActivityLifecycleCallbacks(  lifecycleHandler ) ;
```

##  init 进程
```text
它是内核启动的第一个用户级进程,它的进程号总是1 。
init的入口函数 
// system/core/init/init.cpp
int main(int argc, char** argv) { 
 property_init();  // 对属性进行初始化
 start_property_service();  // 启动属性服务
 parser.ParseConfig("/init.rc");  // 解析init.rc配置文件并启动zygote进程
} 
```

## zygote 进程 ，受精卵 进程
```text
zygote 由 init进程 通过 fork 的方式创建启动的。
zygote 是Android系统上所有应用进程的父进程，我们系统上app的进程都是由这个zygote分裂出来的。
```

## SystemServer 进程
```text
zygote 进程 fork 出 SystemServer 进程

android\internal\os\ZygoteInit.java 
public static void main(String argv[]) {
 startSystemServer(abiList, socketName); // 启动 SystemServer 进程 ，根据反射 fork 出
}
```

## PackageManagerService  服务
```text
由 SystemServer 服务启动 。
// SystemServer.java , startBootstrapServices() 
mPackageManagerService = PackageManagerService.main(... );
```

## ActivityManagerService 服务 、 AMS
```text
由 SystemServer 服务启动 。 所有的APP应用都需要 与 AMS 打交道.

// SystemServer.java , startBootstrapServices() 
mActivityManagerService = mSystemServiceManager.startService( ActivityManagerService.Lifecycle.class).getService();
```

## Launcher 启动流程 、 Launcher3 app
```text
// ActivityManagerService.java  , 
Intent getHomeIntent() {
// 启动  <category android:name="android.intent.category.HOME" /> 的 桌面程序
 intent.addCategory(Intent.CATEGORY_HOME); 
}
```

## Android 系统启动流程
```text
1、内核启动  
2、内核启动 init 进程
3、init 进程  fork  Zygote 进程

4、 zygote 进程 fork 出 SystemServer 进程
5、SystemServer 服务 启动  ActivityManagerService  、 PackageManagerService 服务
6、ActivityManagerService 启动 Launcher app 
```