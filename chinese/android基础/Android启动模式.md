# Android启动模式

#### 多种启动模式的意义
通过使用不同的启动模式，来控制是否生成唯一的activity实例 还是 多个activity实例 。来满足不同的使用场景 。


&nbsp;&nbsp;
&nbsp;&nbsp;

#### 启动模式类型

模式类型 | 意义 | 使用场景
-|-
standard|默认模式。每当有一次Intent请求，就会创建一个新的Activity实例 。并且被放到启动它的那个Activity所属的任务栈中。|如果有10个撰写邮件的Intent，就会创建10个activity给不同的人写信。
singleTop|栈顶复用。栈顶有此实例就使用（调用onNewIntent()方法） ，没有就重新创建。(调用onCreate()方法)|当前正在新闻页面A，此时来了关于新闻A的推送消息，点击推送消息，进入新闻页面A
singleTask|栈内复用。若栈中已有该Activity的实例，就重用该实例(调用onNewIntent()方法)。并且，会将它所在任务栈之前的所有activity实例移除掉。因此该实例就处于栈顶了。若栈中不存在该实例，将会onCreate()。|用来消除交互界面的嵌套循环
singleInstance|全局唯一。Activity单独占用一个Task栈 。整个系统中是单例的。|  还未完全理解使用场景

&nbsp;&nbsp;
&nbsp;&nbsp;

#### 任务栈 （task stack ） 、 后退栈 （back stack）

1、一个应用程序一被启动系统就给它分配一个任务栈 。

2、android存在多个任务栈，一个应用程序一个任务栈。

3、一个任务栈中存放多个activity（可以来自不同的应用程序<进程>）。

&nbsp;&nbsp;
&nbsp;&nbsp;

##### 如何查看当前系统的任务栈
```html
adb shell dumpsys activity
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201803021451_osChina_查看task.png)



#### 不同task之间的关系
相互独立的？ <这个知识点我还不确定>
每次启动新的Activity都将被添加到Activity Stack。用户返回就会将当期的activity实例出栈。如果当前栈空了，就会进入 Home screenn 所在的栈 。



&nbsp;&nbsp;
&nbsp;&nbsp;

#### 指定启动模式 （xml方式）
```xml
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
    android:stateNotNeeded="true"
    />
```

属性 |意义 | 备注
-|-
taskAffinity|指定Activity希望归属的栈|
allowTaskReparenting|任务状态是否始终由系统来维护|默认false
clearTaskOnLaunch||默认false
finishOnTaskLaunch||
alwaysRetainTaskState||

####  指定启动模式 （代码方式）
优先级大于xml方式。
```java
Intent intent = new Intent( this ,TestActivity.class);
intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
this.startActivity(intent);
```
 [官网flags](https://developer.android.com/reference/android/content/Intent.html#addFlags(int)

 Intent的Flag标志 |意义 | 备注
 -|-
 FLAG_ACTIVITY_NEW_TASK| 默认的跳转类型|
 FLAG_ACTIVITY_SINGLE_TOP|singletop模式|
 FLAG_ACTIVITY_BROUGHT_TO_FRONT||
 FLAG_ACTIVITY_CLEAR_TOP|SingleTask模式|
 FLAG_ACTIVITY_NO_HISTORY|Activity不会保留在栈中|
  FLAG_ACTIVITY_NO_ANIMATION|不使用过渡动画|
 FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS|最近应用里面查看不到这个activity的启动记录|
 FLAG_GRANT_READ_URI_PERMISSION||
 FLAG_GRANT_WRITE_URI_PERMISSION||
 FLAG_FROM_BACKGROUND||
 FLAG_DEBUG_LOG_RESOLUTION||
 FLAG_EXCLUDE_STOPPED_PACKAGES||
 FLAG_INCLUDE_STOPPED_PACKAGES||
 FLAG_GRANT_PERSISTABLE_URI_PERMISSION||
 FLAG_GRANT_PREFIX_URI_PERMISSION||
 FLAG_RECEIVER_REGISTERED_ONLY||
 FLAG_RECEIVER_REPLACE_PENDING||
 FLAG_RECEIVER_FOREGROUND||
 FLAG_RECEIVER_NO_ABORT||
 FLAG_ACTIVITY_FORWARD_RESULT||
 FLAG_ACTIVITY_PREVIOUS_IS_TOP||
 FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS||
 FLAG_RECEIVER_VISIBLE_TO_INSTANT_APPS||
 FLAG_ACTIVITY_LAUNCHED_FROM_HISTORY||
 FLAG_ACTIVITY_NEW_DOCUMENT||
 FLAG_ACTIVITY_NO_USER_ACTION||
 FLAG_ACTIVITY_REORDER_TO_FRONT||
 FLAG_ACTIVITY_CLEAR_TASK||
 FLAG_ACTIVITY_TASK_ON_HOME||
 FLAG_ACTIVITY_RETAIN_IN_RECENTS||
 FLAG_ACTIVITY_LAUNCH_ADJACENT||
