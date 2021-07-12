
#  虚拟机对比
## JVM
Java Virtual Machine。基于栈 ，运行java字节码。

## DVM 
```text
Dalvik Virtual Machine 。
运行自谷歌定义的 .dex字节码格式。
Dalvik 基于寄存器的架构， 更适合资源紧张的系统。
```

## ART 
```text
ART , Android runtime 。
Android 4.4 开始加入 ART模式 , 从 Android 5.0 开始 ART 就全面取代了 Dalvik。 
ART下，应用在第一次安装的时候，字节码就会预编译成机器码，首次启动会变慢，
但是以后每次启动执行的时候，都可以直接运行。（空间换时间）
```



# R.java 文件结构 
![](../pics/R文件结构.png)
```text
R.java 定义了res目录中全部资源的id ，
系统会对工程中的 字符串、图片、xml等资源进行搜索生成一个索引文件，
每一个资源对应一个唯一的ID。
将 id 作为 key，去 resource.arsc 里面去查找对应的真正的资源。
这个 ID，用 32 位的 int 表示。格式为 PPTTNNNN。

前 8 位 PP (Package) 表示资源所属包类型，0x7f 表示应用 Apk 包资源，0x01 表示系统包资源。
中间 8 位 TT(Type) 代表资源 id 的类型
0x02：drawable
0x03：layout
0x04：values
0x05：xml
0x06：raw
0x07：color
0x08：menu
最后 16 位表示资源在此类型里面的编号。
```

## R文件的设计目的是啥，为什么不直接使用资源路径
```text
用 R.java 来映射资源文件 ，大概有以下一些优点。
1、编程工具会对res下的资源进行扫描，删除或增加都会自动更新R文件，
维护一个最新的R文件，相当于维护这一个索引，智能提示提高开发者的开发速度。

2、方便引入非文件资源，例如 字符串和颜色配置等。
3、将每个资源都映射成一个32的int (比很长的文件名节省空间) 。 

其他的等待补充。
```

## R 文件的位置
```text
两种情况
1、编码调试阶段 ，在Androidstudio查看 ，
2、打成 apk 后，R文件的位置
```

![](../pics/R文件位置.png)


# .dex 文件 为什么不能超过 65535 个方法
```text
UNEXPECTED TOP-LEVEL EXCEPTION:  
java.lang.IllegalArgumentException: method ID not in [0, 0xffff]: 65536

Java源文件在打包成一个DEX文件，这个文件就是优化过的、Dalvik虚拟机可执行的文件，
Dalvik虚拟机在执行DEX文件时，它使用了 short 这个类型（16 bit）索引DEX文件中的方法，
这意味着单个DEX文件可以被定义的方法最多只能是65535个，当超过这个数量时就会发生编译错误。
```


# 双缓冲
```text
两个线程配合完成某一项工作，一个线程做A部分（例如绘制UI），
另一个线程做B事情（例如读取资源，计算数据）。 

常见的例子：
1、TCP数据传输， 数据的收发速度不一致可能导致数据的丢失。
数据线发送到缓冲区中，然后从缓冲区中取走。
有效地防止了数据的丢失。

2、复杂图像绘制过程中。 
线程A将一帧的计算结果放在缓冲区中，线程绘制完当前帧后再去缓冲区中取下一帧完整的绘制资源。 
这样就可以避免画面闪烁结果。

很多图形操作都很复杂，需要大量的计算，很难访问一次显示缓冲区就能写入待显示的完整图形数据，
通常需要多次访问显示缓冲区，每次访问时写入最新计算的图形数据。
而这样造成的后果是一个需要复杂计算的图形，你看到的效果可能是一部分一部分地显示出来的，造成很大的闪烁不连贯。
```



# 硬件加速
```text
图形的绘制如果是 GPU处理的就是 硬件加速绘制，如果是 CPU 处理的 就是软件绘制。
硬件加速使用 GPU 进行View上的绘制操作。

硬件加速可以在以下四个级别开启或关闭：
Application ：  为整个 APP开启
<application android:hardwareAccelerated="true" ...>

Activity：  activity 范围
<activity android:hardwareAccelerated="false" />

Window： window 范围
getWindow().setFlags( WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED

View：  view 范围
oneView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
```

并非所有的2D绘图操作支持硬件加速。所以硬件加速的开关分为四个层次。
以下是已知不支持硬件加速的绘图操作(需要最新的请查阅[官网](https://developer.android.google.cn/guide/topics/graphics/hardware-accel.html))


## 支持硬件加速情况

Canvas | 第一次支持 | Paint | 第一次支持
-|-
drawBitmapMesh() (colors array) | 18 |setAntiAlias() (for text)	|18
drawPicture()	 | 23 |setAntiAlias() (for lines)|	16
drawPosText() | 16 |setFilterBitmap()|	17
drawTextOnPath() | 16 |setLinearText()|	✗
drawVertices() | ✗ |setMaskFilter()|	✗
setDrawFilter() | 16 |setPathEffect() (for lines)	|✗
clipPath() | 18 |setRasterizer()	|✗
clipRegion() | 18 |setShadowLayer() (other than text)	|✗
clipRect(Region.Op.XOR) | 18 |setStrokeCap() (for lines)	|18
clipRect(Region.Op.Difference) | 18 |setStrokeCap() (for points)	|19
clipRect(Region.Op.ReverseDifference) | 18 |setSubpixelText()	|✗
clipRect() with rotation/perspective| 18 | |



Xfermode | 第一次支持 |Shader | 第一次支持
-|-
PorterDuff.Mode.DARKEN (framebuffer)	|✗|ComposeShader inside ComposeShader	|✗
PorterDuff.Mode.LIGHTEN (framebuffer)|	✗|Same type shaders inside ComposeShader	|✗
PorterDuff.Mode.OVERLAY (framebuffer)|	✗  | Local matrix on ComposeShader	|18


Drawing operation to be scaled	|第一次支持
-|-
drawText()	|18
drawPosText()	|✗
drawTextOnPath()	|✗
Simple Shapes*	|17
Complex Shapes*	|✗
drawPath()	|✗
Shadow layer	|✗

# SharedPreferences
一些简单的、无安全风险的键值对数据，可以通过 SharedPreferences 保存。
SharedPreferences 是一个轻量级的xml键值对文件 。
```text
文件保存在 /data/data/<package name>/shared_prefs 中，明文可见。
用真机中的文件夹管理器一般看不到，可以用过 Androidstudio 去看。

在 SharedPreferencesImpl.java 中
private void startLoadFromDisk() {
    new Thread("SharedPreferencesImpl-load") {
    ...
            loadFromDisk(); // 开启子线程读取
    }.start();
}
```

## SharedPreferences  apply 和  commit 的区别
```text
apply()：提交后同步写入内存，然后异步写入磁盘，没有返回值。
commit()：需要等异步回写磁盘完成后才返回，有返回值。
如果频繁操作的话 apply 的性能会优于 commit 。 
```

## SharedPreferences   是线程安全的吗 
```text
SharedPreferences 是线程安全的，因为内部有 synchronized 关键字保障。
```


## SharedPreferences 是进程安全的吗？ 如何保证进程安全  
```text
因为 SharedPreferences 会从内存取值，但是进程间内存不是共享的，所以不是多进程安全的。
// SharedPreferencesImpl.java
public String getString(String key, @Nullable String defValue) {
    synchronized (mLock) {
        awaitLoadedLocked();
        String v = (String)mMap.get(key); // 从内存取值
        return v != null ? v : defValue;
    }
}

SharedPreferences sharedPreferences =
context.getSharedPreferences("xxxName", Context.MODE_MULTI_PROCESS);
// 因为对多进程支持不完善，MODE_MULTI_PROCESS (也不可靠) 模式也已经被废弃，
//  官方推荐 ContentProvider 或者 第三方框架 MMKV
```


# fork 是什么意思
```text
经常会听到 fork 一个进程，初学者有时不太理解。

fork() 函数用于从一个已经存在的进程内创建一个新的进程，
使用 fork() 得到的子进程是父进程的复制品，
子进程完全复制了父进程的资源，
包括进程上下文、代码区、数据区、堆区、栈区、内存信息、打开文件的文件描述符、
信号处理函数、进程优先级、进程组号、当前工作目录、根目录、资源限制和控制终端等信息，
而子进程与父进程的区别有进程号、资源使用情况和计时器等。

从基本作用上来理解， fork 跟 Java里的深克隆类似。
都是从一个目标完全复制一份新的对象出来。

github 上也有有一个 fork ，
感觉也类似，就是从别人的代码那里，
复制一份一模一样的代码到你的账号上。
```

# hook 是什么意思
```text
hook 的中文翻译是钩子。
在windows系统中，一切皆消息，比如按了一下键盘，也是一个消息。
Hook的意思是勾住 ，也就是在消息过去之前，可以先把消息勾住，
不让其传递，你可以优先处理。

hook 技术就是提供了一个方案，
能够针对不同的消息或者API在执行前，先执行你的操作，
你的操作也称为“钩子函数”，
所以有的时候程序员在讨论的时候，也经常会说，可以先hook住，再处理，
也即在执行某某操作之前，优先处理一下
```


# Application 
```text
// Application 中显示 Dialog (AlertDialog) 
借助 ActivityLifecycleCallbacks 的生命周期回调 获得 Context
new AlertDialog.Builder(mContext);   

// 通过 Application  传值 缺点 
```
 
 
# AlertDialog 按钮大小问题
```text
TextView textView =new TextView(this);
textView.setText("Hello TextView");

AlertDialog dialog =new AlertDialog.Builder(this)
        .setTitle("Hello aivin")
        .setView(textView)
        .setPositiveButton("ok_Btn", null )
        .show() ;
Button btn = dialog.getButton(AlertDialog.BUTTON_POSITIVE) ;
btn.setAllCaps(false); // 重新设置
``` 
 
 #  ListView 动态改变高度
 ```text
 public void setListViewHeightBasedOnChildren2(ListView listView) {
     ListAdapter listAdapter = listView.getAdapter();
     if (listAdapter == null) {
         return;
     }
     int totalHeight = 0;
     for (int i = 0; i < listAdapter.getCount(); i++) {
         View listItem = listAdapter.getView(i, null, listView);
         // 对 子View item 不做限制，要多大就报上来多大
         listItem.measure(View.MeasureSpec.UNSPECIFIED, View.MeasureSpec.UNSPECIFIED);
         totalHeight += listItem.getMeasuredHeight();
     }
 
     ViewGroup.LayoutParams params = listView.getLayoutParams();
     params.height = totalHeight + (listView.getDividerHeight() * (listAdapter.getCount() -1));
     listView.setLayoutParams(params);
 }
 ```





# 约束布局 ConstraintLayout
```text
api 'androidx.constraintlayout:constraintlayout:2.0.2'

<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:layout_constraintLeft_toLeftOf="parent"
    app:layout_constraintRight_toRightOf="parent"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintBottom_toBottomOf="parent" />

// 角度约束 (版本1.1中加入)
一个控件在某个控件的某个角度的位置，通过其他的布局其实是不太好实现的，
但是 ConstraintLayout 提供了角度位置相关的属性
app:layout_constraintCircle=""         目标控件id
app:layout_constraintCircleAngle=""    对于目标的角度(0-360)
app:layout_constraintCircleRadius=""   到目标中心的距离

// 百分比偏移
让控件在父布局的水平方向或垂直方向的百分之多少的位置，可以使用如下属性：
app:layout_constraintHorizontal_bias=""   水平偏移 取值范围是0-1的小数
app:layout_constraintVertical_bias=""     垂直偏移 取值范围是0-1的小数

// A依赖B ，B被Gone时，A距离父控件的距离。
app:layout_goneMarginBottom="0dp"
app:layout_goneMarginEnd="0dp"
app:layout_goneMarginLeft="0dp"
app:layout_goneMarginRight="0dp"
app:layout_goneMarginStart="0dp"
app:layout_goneMarginTop="0dp"

//设置宽高比例
给宽或者高其中一个设置为0dp，然后设置该属性是一个比例，宽和高的比
layout_constraintDimensionRatio

// Chain 链
Chain 链是一种特殊的约束让多个 chain 链连接的 Views 能够平分剩余空间位置。
最相似的应该是 LinearLayout 中的权重比 weight ，
但 Chains 链能做到的远远不止权重比 weight 的功能。
Chain 链是由多个 Views 组合的，所以要创建一个 Chain 链就需要先选择多个想要链接到一起的 Views ，
然后再右键选择 'Center Horizontally' 或者 'Center Vertically' 来创建水平链或者垂直链。
Chain 链的创建定义的是 Chain 链组件之间的间隙关系，并不影响原有的非成员间的约束。

Chain 链模式一共有三种，分别为：spread ，spread_inside 和 packed 。
1、默认 spread 模式：
将平分间隙让多个 Views 布局到剩余空间。
2、spread_inside 模式：
它将会把两边最边缘的两个 View 到外向父组件边缘的距离去除，
然后让剩余的 Views 在剩余的空间内平分间隙布局。
3、 packed ：
它将所有 Views 打包到一起不分配多余的间隙 ，然后将整个组件组在可用的剩余位置居中。
```


 
# 序列化
```text
序列化，将内存中保存的是对象以二进制数据流的形式进行处理，
可以实现对象的保存或者是网络传输。
与序列化相对的是反序列化，它将流转换为对象。
```

## Bunder 传递对象为什么需要序列化
```text
因为 Bunder + intent 是支持跨进程传递的，
而 Android 进程间是不支持 对象传递的，
所以要将对象序列化成二进制。
```

## serialVersionUID 的作用
```text
serialVersionUID 主要是一种安全机制。

序列化的时候系统会把当前类的 serialVersionUID 写入序列化文件中，
当反序列化的时候系统会去检测文件中的serialVersionUID,看他是否和当前类的一致，如果一致，
说明序列化类的版本和当前类的版本是相同的，这个时候成功反序列化，
否则说明当前类和序列化的类相比发生某些变换，这个时候无法正常反序列化，会报错。
 
如果不手动指定 serialVersionUID ，那么将对象序列化时系统生成一个 serialVersionUID值 一起持久化了。
此时如果你给这个对象增加了一个属性，再次持久化时，此时生成 serialVersionUID 值是不一样的。

如果你不想进行这样严格的校验，你手动手动 serialVersionUID =1L ，那么即使你新增或者减少了属性，
反序列化的时候也不会报错，只是被忽略或者默认置为0 。 
 
Androidstudio中生成  serialVersionUID的方法。
preferences->Inspections->serialization issues->Serializable class without 'serialVersionUID' 勾上 。
此时会提示生成严格验证的 serialVersionUID的方法 。如果不想严格校验可以直接写成1 。
```

## Serializable 与 Parcelable 的区别
```text
Serializable 是一种标识接口,是一个空接口。
对某个类实现 Serializable 后，Java便会对这个对象进行序列化操作。
这种方法使用了反射原理，序列化的过程较慢。
会在序列化的时候创建许多的临时对象，可能会引起频繁的GC。
使用起来简单但是开销很大，序列化和反序列化过程需要大量I/O操作。
Serializable 主要用于持久化存储对象 ，保存在磁盘或者网络传输。

Parcelable 方式的实现原理是将一个完整的对象进行分解， 
而分解后的每一部分都是Intent所支持的数据类型，这样也就实现传递对象的功能了。
因为我们已经清楚地知道了序列化的过程，而不需要使用反射来推断。
为了这种高效分解组合，我们需要按要求进行一些代码书写。

Parcelable 是直接在内存中读写 ，
无法将数据进行持久化 ，只能用于程序内内存间进行对象传输 。

在 Androidstudio 中，我们可以使用 插件 " Parcelable code generator " 
来自动生成一些代码。减少代码书写量。
``` 


# Activity 与 AppCompatActivity 区别
```text
随着 Android系统的更新，增加了一些新的特性，
所以后续慢慢地推出了  FragmentActivity 、AppCompatActivity  。
AppCompatActivity 加入了很多新特性，并且向下兼容(兼容老设备,旧系统版本)。

AppCompatActivity extends FragmentActivity 
FragmentActivity extends ComponentActivity 
ComponentActivity extends Activity

// Activity
Activity是最基础的一个，是其它类的直接或间接父类。
Activity中 只能使用系统自带的 FragmentTabHost+Fragment  ， 
用 getFragmentManager() 来控制Activity和Fragment之间的交互。

// FragmentActivity
在v4包中引 入 FragmentActivity ，
FragmentActivity 间接继承自Activity，并提供了对v4包中 support Fragment的支持。
在 FragmentActivity 中必须使用 getSupportFragmentManager() 来处理support Fragment的交互。

// AppCompatActivity
AppCompatActivity 继承自FragmentActivity，同时取代了ActionBarActivity。
AppCompatActivity 支持 ActionBar 功能，同时更推荐使用 ToolBar。
而且 AppCompatActivity 为支持Material Design风格控件提供了便利。

另外：与 Activity 相比较， AppCompatActivity 的 theme 只能用 android:theme=”@style/AppTheme ，而不能用android:style。 
否则会提示错误： Caused by: java.lang.IllegalStateException: 
You need to use a Theme.AppCompat theme (or descendant) with this activity.
```


# android 严苛模式 、严格模式
```text
在代码中配置严苛模式，用来帮助我们更容易找到违规的代码。
比如在主线程中访问网络、访问磁盘、Activity的泄露 等不合理的操作。

// 线程策略
StrictMode.ThreadPolicy threadPolicy = new StrictMode.ThreadPolicy.Builder()
        .detectAll() // 检测所有潜在的违例
        .detectCustomSlowCalls()// 自定义耗时操作
        .detectDiskReads() // 读磁盘
        .detectDiskWrites() // 写磁盘
        .detectNetwork() // 检查网络
        .detectResourceMismatches() // 检查资源类型是否匹配
        .penaltyLog() // 将警告输出到LogCat
        .penaltyFlashScreen()// 屏幕闪烁
        .penaltyDropBox() // 将违规信息记录到 dropbox 系统日志目录中（/data/system/dropbox）
        .penaltyDeath() // 当触发违规条件时，直接Crash掉当前应用程序。
        .penaltyDialog() // 弹出dialog
        .penaltyDeathOnNetwork()// 当触发网络违规时，Crash掉当前应用程序
        .build();

// Vm 策略
StrictMode.VmPolicy vmPolicy = new StrictMode.VmPolicy.Builder()
        .detectAll() // 检测所有潜在的
        .detectActivityLeaks()// 检测Activity的泄露
        .detectCleartextNetwork() //检测明文的网络
        .detectFileUriExposure() //  检测file:// 或 content://
        .detectLeakedClosableObjects()  // 未关闭的Closable对象泄露
        .detectLeakedRegistrationObjects() // 检测需要注册类型是否解注
        .detectLeakedSqlLiteObjects() // 泄露的Sqlite对象
        .setClassInstanceLimit(Stutent.class ,10)// 检测实例数量
        .penaltyLog() //  将警告输出到LogCat
        .penaltyDropBox() // 将违规信息记录到 dropbox 系统日志目录中（/data/system/dropbox）
        .penaltyDeath() // 当触发违规条件时，直接Crash掉当前应用程序。
        .build();

StrictMode.setThreadPolicy(threadPolicy );
StrictMode.setVmPolicy(vmPolicy  );
```


# 线程休眠方法
```text
Thread.sleep()  // 抛异常 ,可能会被中断  
SystemClock.sleep( ) // 不能被中断 ，Android 推荐用这个
LockSupport.parkNanos(1);//  LockSupport.unpark(thread);       
object.wait() // object.notifyAll();  不推荐使用
```


# Android 拖拽效果
```text
以前没看过这个知识点，以为绘制那个虚影有点麻烦。
其实 Android api 有提供一个简单的 api view.startDrag( ) ，会自动生成虚影。
在实际项目中，配合处理一下 拖动事件的监听和坐标的处理即可。
可以参考一下这个项目 
https://gitee.com/hnyer/RemoteControlView
--------------------

public void startDrag(View view ,DraggableInfo draggableInfo ){
    Intent intent = new Intent();
    intent.putExtra(MyConfig.KEY_DATA, draggableInfo);
    //用 ClipData 来跨 activity 传值
    ClipData dragData = ClipData.newIntent(MyConfig.KEY_Value, intent);
    View.DragShadowBuilder myShadow = new View.DragShadowBuilder(view);
    // 震动 (不需要震动权限)
    view.performHapticFeedback(HapticFeedbackConstants.LONG_PRESS, HapticFeedbackConstants.FLAG_IGNORE_GLOBAL_SETTING);
    // 执行startDrag后 ，系统就会生成view的拖拽影子
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
        view.startDragAndDrop(dragData, myShadow, null, 0);
    }else{
        view.startDrag(dragData, myShadow, null, 0);
    }
}
// 给一个控件添加拖动监听，可以监听到被拖动的那个控件的拖动事件和坐标
frameLayout.setOnDragListener(onDragListener);
private  View.OnDragListener onDragListener = new OnDragListener() {
    public boolean onDrag(View v, DragEvent event) {
        final int action = event.getAction();
        switch(action) {
            case DragEvent.ACTION_DRAG_STARTED:  // 开始拖动
                break;
            case DragEvent.ACTION_DRAG_ENTERED:  //  进入
                break;
            case DragEvent.ACTION_DRAG_EXITED:  //  移出
                break;
            case DragEvent.ACTION_DRAG_ENDED:  // 停止拖动
                break;
            case DragEvent.ACTION_DRAG_LOCATION: // 停留
                break;
            case DragEvent.ACTION_DROP: // 释放拖动
                break;
        }
        return true;
    }
};
```

![](../pics/拖拽效果.gif)


