# Android启动模式

## activity 生命周期
### onDestroy
```text
// onStop 和 onDestroy 回调为什么会延时
由于要关闭的 或者要打开的 Activity 往主线程的 MessageQueue 中发送了 大量的 Message ,
导致主线程一直在不断的进行消息循环处理这些消息而没有得到停歇。
App 侧就不能向 AMS 告知自己有空闲时间来处理 AMS 侧的任务。
所以，finish Activity B后，onDestroy不会被及时回调。
具体延时多久，要看主线程什么时候闲下来。

// 为什么 Activity.finish() 之后 10s 才 onStop 、onDestroy 
除正常流程外，Android 系统另行安排了一套流程来保证即使正常流程被阻断以后，Activity B还是能被销毁。
ActivityStac.java 中 
// schedule an idle timeout in case the app doesn't do it for us.
mStackSupervisor.scheduleIdleTimeoutLocked(next);
this.mHandler.sendMessageDelayed(msg, 10000L); // 10s
```

### onRestart
经过测试，以下几种情况 onRestart()方法 会被调用 
```text
1、按下home键之后，然后切换回来  
2、从本Activity跳转到另一个Activity之后，按 back键返回原来 Activity 
3、从本Activity切换到其他的应用，然后再从其他应用切换回来 
``` 

### onStop
```text
两个Activity之间跳转 ,
从A跳到B ，A的 onStop 是否执行 取决于 B是否完全覆盖在 A上 导致A整个不可见。  
如果 B 是透明的 Activity 或者是个 Dialog 样式 ，A 的 onStop 并不会被执行。
```

### 按 Back 键 后，activity 的生命周期变化
```text
这些东西 写个简单demo 实验一把 当场就能出结果。
面试官要是硬要你回答，就是沙比。
```

### 按 Home 键 后，activity 的生命周期变化
```text
这些东西 写个简单demo 实验一把 当场就能出结果。
```

### activity 启动另外一个activity的时候横屏切换再点击返回，过程中发生的生命周期
```text
这些东西 写个简单demo 实验一把 当场就能出结果。
```

## 启动模式
通过使用不同的启动模式，来控制是否生成唯一还是多个activity实例 。来满足不同的使用场景 。

###  standard 模式
```text
默认模式。每当有一次Intent请求，就会创建一个新的Activity实例 。
并且被放到启动它的那个Activity所属的任务栈中。
```

### singleTop 模式
```text
栈顶复用。
栈顶有此实例就使用（调用 onNewIntent()方法） ，没有就重新创建。(调用onCreate()方法)
```

### singleTask 模式
```text
栈内复用。
若栈中已有该Activity的实例，就重用该实例(调用onNewIntent()方法)。
并且会将它所在任务栈之前的所有activity实例移除掉。因此该实例就处于栈顶了。
若栈中不存在该实例，将会onCreate()。
可以用在 消除交互界面的嵌套循环 场景。
```

### singleInstance 模式
```text
全局唯一。
Activity单独占用一个Task栈 。整个系统中是单例的。
```

### 启动模式属性
```text
// taskAffinity 
android:taskAffinity="task001"
它倾向于将 taskAffinity 属性相同的Activity，扔进同一个Task中。指定Activity希望归属的栈

// allowTaskReparenting
任务状态是否始终由系统来维护
android:allowTaskReparenting="true"  //默认false
```
 

##  任务栈 task stack  
每次启动新的Activity都将被添加到Activity Stack。
用户返回就会将当期的activity实例出栈。
如果当前栈空了，就会进入 Home screenn 所在的栈 。
```text
1、一个应用程序一被启动,系统就给它分配一个任务栈 。 一个应用程序一个任务栈。
2、一个任务栈中存放多个activity ,可以来自不同的应用程序。
```

### 查看  系统的任务栈 、 Activity 栈信息  、各个栈的 Activity 列表
```text
adb shell dumpsys activity
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201803021451_osChina_查看task.png)

 

## 指定启动模式  
```text
代码方式 优先级大于 xml方式 
// xml方式
<activity android:name=".MyLauchModeActivity"
    android:launchMode="singleTop"
    android:taskAffinity="com.test.."
    android:allowTaskReparenting="true"
    android:clearTaskOnLaunch="true"
    android:finishOnTaskLaunch="true"
    android:alwaysRetainTaskState="true"
    android:multiprocess="true"
    android:noHistory="true"
    android:excludeFromRecents="true"
    android:process=""
    android:stateNotNeeded="true" />

// 代码方式
Intent intent = new Intent( this ,TestActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
this.startActivity(intent);
```
 
## Intent的 Flag标志
 [官网flags](https://developer.android.com/reference/android/content/Intent.html#addFlags(int)

 Intent的Flag标志 |意义 
 -|-
 FLAG_ACTIVITY_NEW_TASK| 默认的跳转类型 
 FLAG_ACTIVITY_SINGLE_TOP|singletop模式 
 FLAG_ACTIVITY_BROUGHT_TO_FRONT| 
 FLAG_ACTIVITY_CLEAR_TOP|SingleTask模式 
 FLAG_ACTIVITY_NO_HISTORY|Activity不会保留在栈中 
 FLAG_ACTIVITY_NO_ANIMATION|不使用过渡动画
 FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS|最近应用里面查看不到这个activity的启动记录

