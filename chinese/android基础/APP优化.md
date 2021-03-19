# 性能优化
应用的性能优化，需要建立一套成体系的性能优化方案，
这套方案被业界称为 APM (Application Performance Manange)。

##  UI流畅度优化 、界面卡顿 排查及优化
Skipped 60 frames!  The application may be doing too much work on its main thread.
```text
在大部分Android平台的设备上，Android系统是 16ms (1000 /60 = 16.67 ) 刷新一次，也就是一秒钟60帧。 
要达到这种刷新速度就要求在ui线程中处理的任务时间必须要小于16ms，如果ui线程中处理时间长，
就会导致跳过帧的渲染，也就是导致界面看起来不流畅，卡顿。
```

## 卡顿引起的具体原因
```text
1、cpu 占用过高，容易卡顿 。一般是 后台线程处理的东西太繁忙。
注意逻辑的优化，线程不要空跑。

2、主线程 绘制时间过长。
UI的层级别太大 ，不要冗余嵌套 
```

## 卡顿检测 Choreographer
```text
使用 Androidstudio 自带的 工具，和一些第三方的监控工具 例如 BlockCanary 就差不多了。

// FPS ( Frames Per Second )
即 Frame Rate，单位 fps，是指 gpu 生成帧的速率 ，Android中更帧率相关的类是 SurfaceFlinger 。
SurfaceFlinger (SurfaceFlinger.h) 是Android的一个 native进程 ，
接受多个来源的图形显示数据，将他们合成，然后发送到显示设备。

// VSync (Synchronization ) ,垂直同步 信号。
Android系统每隔16ms发出 VSync 信号，触发对UI进行渲染，
Android 4.1 开始引入 VSync 机制，用来同步渲染，
让 UI 和 SurfaceFlinger 可以按硬件产生的 VSync 节奏进行工作。

WkHeartBeatTool wkHeartBeatTool =  new WkHeartBeatTool();
wkHeartBeatTool.startTheBeatAction(new HeartBeatTask() {
    @Override
    public void run() {
        // 一秒钟统计一次 ，如果小于 60 ，就说明掉帧了
        WkLogTool.showLog("fps===="+count);
        count= 0 ;
    }
} ,1000);

// Choreographer 编舞者 ，统计一秒内 count 的数量 ，
Choreographer.getInstance()
        .postFrameCallback( new Choreographer.FrameCallback() {
            // frameTimeNanos: The time in nanoseconds when the frame started being rendered,
            @Override
            public void doFrame(long frameTimeNanos) {
                count++ ;
                Choreographer.getInstance().postFrameCallback(this);
            }
        });
```

## 排查 线上App ,用户反馈卡顿的问题
```text
1、记录用户的使用机型和使用场景。例如操作流程、网络环境
2、加入卡顿检测机制，有第三方的 SDK 也可以自己写 ，
将卡顿时的堆栈信息记录并回传，定位分析。
```



## apk瘦身
最主要的是 从图片资源 、第三方库 和 .so 方面考虑 。
```text
1、 用lint检查， 删除无用资源
2、 用tinypng等压缩图片。
3、用webp格式图片
4、开启代码混淆
5、删除冗余代码。
```

# 布局优化
```text

```


# 启动优化
## 启动类型
```text
// 冷启动 
冷启动就是从0开始启动 App 。
从点击应用图标到UI界面完全显示且用户可操作的全部过程。

// 温启动
当启动应用时，后台已有该应用的进程，
在已有进程的情况下，会从已有的进程中来启动应用 。
只会重走 Activity 的生命周期，而不会重走进程的创建，不走 Application 的创建与生命周期等。

// 热启动
直接从后台切换到前台。
```

```text
// 冷启动优化
1、Application中对 第三方的SDK进行异步或延时初始化 。
2、做一个闪屏界面。在展示的这段时间里，去加载下一页需要的资源。
// 热启动优化
在app 退出时 不要finish ，而是 moveTaskToBack ，即模拟 HOME按键的事件 。
```

 
 
 

# 卡顿优化
```text

```


# 绘制优化
```text

```


# IO 优化
```text

```



# 存储优化
## 需要优化存储的原因
```text
1、储存耗时较长
2、线程不安全
3、有潜在的 ANR 
4、不支持跨进程。
```

## 常用的存储方案
```text
1、SharedPreferences
2、 DataStore
DataStore 是 Jetpack 中的一员。

3、MMKV 
4、SQLite
5、文件存储


```







# 网络优化
```text

```

# 内存优化 (完成)
```text
1、别频繁GC
2、控制好对象的引用，防止对象一直无法释放。

需要注意的是，出现OOM是因为内存溢出导致，
但是这种情况不一定会发生在相对应的代码处，
也不一定是出现OOM的代码使用内存有问题，而是刚好执行到这段代码。

内存泄漏就是在当前应用周期内不再使用的对象被 GC Roots 引用，
导致不能回收，使实际可使用内存变小。
```


## 内存优化的作用
```text
1、减少OOM，提高应用稳定性。
2、减少卡顿，提高应用流畅度。
3、减少内存占用，提高应用后台运行时的存活率。
```

## 常见内存泄漏场景
### 资源性对象未关闭
```text
对于资源性对象不再使用时，应该立即调用它的close()函数，将其关闭，然后再置为null。
例如Bitmap等资源未关闭会造成内存泄漏，此时我们应该在 Activity 销毁时及时关闭。
```

###  注册对象未注销
```text
例如 BraodcastReceiver 、EventBus 未注销造成的内存泄漏，
我们应该在Activity销毁时及时注销。
```

###  类的静态变量持有大数据对象
```text
尽量避免使用静态变量存储数据，特别是大数据对象，建议使用数据库存储。
```


###  单例造成的内存泄漏
```text
优先使用Application的Context，
如需使用Activity的Context，可以在传入Context时使用弱引用进行封装 。
```

### Handler 内存泄漏
请查看 Handle 详解 这一块。



### AsyncTask 内存泄露
```text
AsyncTask 的内存泄漏的原因跟 Handle 原因类似。
由于 持有外部类 activity 的强引用 ，
如果 activity 退出时， AsyncTask 还在执行操作，导致 activity 无法释放。
解决办法有2个：
1、在退出是 手动调用  asyncTask.execute() 
2、static + WeakReference 
private static class MyTask extends AsyncTask<Bundle, Integer, Bundle> {
    private final WeakReference<MainActivity> weakReference;
    private MyTask(MainActivity activity) {
        weakReference = new WeakReference<>(activity);
    }
    @Override
    protected Bundle doInBackground(Bundle... bundles) {
        return bundle; // 耗时操作
    }
    @Override
    protected void onPostExecute(Bundle bundle) {
        if (weakReference.get() == null){
            return;
        }
        weakReference.get().handleResult(bundle);
    }
}
```


###  容器中的对象没清理造成的内存泄漏
```text
及时将集合里的东西clear，然后置为null，再退出。
否则会造成集合越来越大，万一静态集合忘记退出和清空了，会造成内存泄漏。
```


### WebView Bug 造成内存泄漏
```text
WebView 因为bug ，都存在内存泄漏的问题。
我们可以为 WebView开启一个独立的进程，使用AIDL与应用的主进程进行通信，
WebView所在的进程可以根据业务的需要选择合适的时机进行销毁，
达到正常释放内存的目的。
```


### ListView 使用不当造成内存泄漏
```text
如果不使用缓存 convertView 的话，调用getView时每次都会重新创建View，
这样之前的View可能还没有销毁，
加之不断的新建View势必会造成内存泄露。
```


## 内存优化方案
###  减少自动装箱和拆箱 ，Autoboxing and unboxing 
```text
Integer total = 99;//自动装箱
int totalprim = total; //自动拆箱
在自动装箱转化时，都会产生一个新的对象，这样就会产生更多的内存和性能开销。
```


### 内存复用
```text
1、资源复用：通用的字符串、颜色定义、简单页面布局的复用。
2、视图复用：可以使用ViewHolder实现ConvertView复用。
3、对象池：显示创建对象池，实现复用逻辑，对相同的类型数据使用同一块内存空间。
4、Bitmap对象的复用： 使用 inBitmap 选项
```


### 使用最优的数据类型
```text
1、 ArrayMap 比 HashMap 更省内存。  
 
2、使用 IntDef 和 @IntDef @StringDe 替代枚举类型
枚举最大的优点是类型安全 ，但是很消耗内存。
```



### 使用合理的缓存策略 LruCache (Least Recently Used)
```text
它内部维护了一个队列，每当从中取出一个值时，该值就移动到队列的头部。
当缓存已满而继续添加时，会将队列尾部的值移除，方便GC。
LruCache用于内存缓存，在避免程序发生OOM和提高执行效率有着良好表现。
```



### 图片内存优化
```text
具体跳转到 “图片加载详解” 一章查看。
```



### 列表 item 被回收不可见时释放掉对图片的引用
```text
ListView：因此每次item被回收后再次利用都会重新绑定数据，
只需在ImageView onDetachFromWindow的时候释放掉图片引用即可。

RecyclerView：因为被回收不可见时第一选择是放进mCacheView中，
这里item被复用并不会只需bindViewHolder来重新绑定数据，
只有被回收进mRecyclePool中后拿出来复用才会重新绑定数据，
因此重写Recycler.Adapter中的onViewRecycled()方法
来使item被回收进RecyclePool的时候去释放图片引用。
```

### 禁用字符串的拼接
```text
我们可以在字符串拼接的时候使用StringBuffer，StringBuilder。
代替 字符串拼接。
```

### 自定义View中的内存优化
```text
在 onDraw 方法等频繁调用的函数里面不要执行对象的创建，
一般来说，都应该在自定义View的构造器中创建对象。
```




### final 优化 (不同意)
```text
我看到有博客说是 用 static final 修饰字段会优化内存。
但是在参考了一些其他资料后，没有发现 final 跟内存优化有关。

使用final方法的原因有2个， 
1、锁定，禁止再修改
2、高效。编译器在遇到调用 final 时会转入内嵌机制，提高执行效率。 

调用一个函数除了函数本身的执行时间之外，还需要额外的时间去寻找这个函数。
所以减少函数调用次数就等于降低了性能消耗。
编译器直接将 final 函数体内嵌到了调用函数的地方，这样的结果是节省了寻找函数的时间。
```


 
 



# 包体积优化 (完成)
## 优化 APK 体积的意义
```text
1、瘦身优化最主要的好处是对应用 下载转化率 的影响，
如果你的 App 与其它同类型的 App 相比 Apk 体积要更小的话，
那么你的 App 下载率就可能要高一些。
包体积越小，用户下载等待的时间会越短，下载转换成功率就越高。

2、有些应用商店对APP的大小有限制，
以谷歌应用商店为例，就有150M 的限制。
With Android App Bundles, the compressed download size restriction is now 150MB 。（2021-3-17 记录，以后可能会改）

3、一些渠道合作商的要求，比如预装应用，限制APP大小或者根据大小收费。

4、apk 过大 对用户的使用体验有影响。
文件的拷贝、解压、初始化 会消耗比较常的时间，让用户等待过久。
同时也占用过大的SD空间，例如王者荣耀占用空间四五个G，是我卸载它的原因。
```

## apk 的组成
```text
1、代码相关 classes.dex
2、资源相关：res、assets ，存放图片和音频等。 优化空间较大，尤其是图片。
3、lib 、So 相关。这一块的优化空间比较大。   
```

## APK 体积优化方案
### Proguard 代码混淆 (建议)
```text
Android 有自带的混淆工具 Proguard  。
将类中的字段、方法、类的名称改成简短无意义的名字。
例如 ActivityMainBinding 被混淆替换成了 一个字母 a .
混淆后，增加代码被反编译的难度，一定程度上保证代码的安全。
并且减少了代码的体积。

同时 Proguard 可以检测并移除未使用到的类、方法、字段以及指令、
冗余代码，并能够对字节码进行深度优化。
```

###  R8 代码混淆
https://r8.googlesource.com/r8
```text
R8 是 Proguard 压缩与优化部分的替代品，并且它仍然使用与 Proguard 一样的 keep 规则。

当使用 Android Gradle 插件 3.4.0 或更高版本构建项目时，
不再使用 ProGuard 来执行编译时代码优化，而是与 R8 编译器协同工作来处理编译时任务 。

R8 与 Proguard 比较 ，
R8 在 inline 内联容器类中更有效，并且在删除未使用的类，字段和方法上则更具侵略性。
并且 ，R8 进行了 ProGuard 尚未提供的一些 Kotlin 的特定的优化。
```


### 使用 D8 编译器
```text
Google 在 Android Studio 3.1 版本中引入 D8 编译器作为默认的 DEX 字节码文件编译器。
通过在 gradle.properties 中新增 android.enableD8=true 开启 D8 编译器。

D8 编译器特点是：
编译更快、时间更短；
DEX 编译时占用内容更小；
.dex 文件大小更小；
D8 编译的 .dex 文件拥有相同或者是更好的运行时性能；
```

### redex 去除 debug 信息与行号信息 
```text
-keepattributes SourceFile,LineNumberTable ,
如果在混淆开启后，开启了这个语句，就会保留 dex 中的  debug 和 行号信息,用于混淆后定位错误位置。

所以我们可以想办法去掉行 dex 中的debug 和行号信息来减小 apk 体积。
有什么方式可以去掉  debugItem，同时又能让 crash 上报的时候能拿到正确的行号呢？
我们可以尝试直接修改 Dex 文件，保留一小块  debugItem，
让系统查找行号的时候指令集行号和源文件行号保持一致。
具体操作可以使用第三方工具，redex 。
```


### redex ， Dex 分包优化
```text
当 app 的方法数超过65536个，就要采用 mutildex 进行分包，
但是此时每一个 Dex 可能会调用到其它 Dex 中的方法，这种 跨 Dex 调用的方式会造成许多冗余信息。
为了减少跨 Dex 调用的情况，我们必须 尽量将有调用关系的类和方法分配到同一个 Dex 中。

ReDex 的 CrossDexDefMinimizer 类分析了类之间的调用关系，
并使用了贪心算法去计算局部的最优解（编译效果和dex优化效果之间的某一个平衡点）。
使用 "InterDexPass" 配置项可以把互相引用的类尽量放在同个 Dex

我们可以使用 facebook 提供的 redex 来进行优化
https://github.com/facebook/redex
ReDex 可以分包优化、去除 debug 信息及行号信息。
```

### XZ Utils 进行 Dex 压缩
```text
https://tukaani.org/xz/ 
有些大公司(facebook ?)在用 xz utils 对 Dex 进行压缩优化，
不过难度较大。
```

### 去掉冗余的第三方库 (建议)
```text
比如有些库会提供 图片加载、网络访问、数据等，
但是你只需要要用到 数据库，你就可以将其他的没用上的库去掉。
可以 只引入部分需要的代码，而不是将整个包的代码都引入进来。
很多库的代码结构都设计的比较好，比如 Fresco，它将图片加载的各个功能，如 webp、gif 功能进行了剥离，
它们都处于单个的库当中。

同时注意同一个APP中尽量不要有相同的库，
比如不要有两个图片加载库。
```

### 删除无用代码、无用资源 (建议)
```text
可以借助 lint 工具等 检测出无用的代码和资源，删除减少包体积。

文件相同，文件名不同。可以通过MD5 去识别。
```


### 自带的 资源去重 配置 
```text
// 防止同名资源
1、从源头控制，配置 resourcePrefix 指定前缀，
xml资源会红色提示(但是不影响运行)你要加前缀。
图片资源没有提示，需要有意识地添加。
android {
    resourcePrefix "app_"
}

2、图片相同，名字不一样。文件去重 方法。
这里写了一个 Md5文件去重 示例。
https://gitee.com/Aivin_CodeShare/android_tool_code/raw/master/md5%E6%96%87%E4%BB%B6%E5%8E%BB%E9%87%8D/CalcMD5.java
```

### 图片压缩、图片格式 (建议)
```text
可以使用 https://tingpng.com/ 压缩图片大小。

图片格式不同，也会导致大小相差较大。
可以根据以下规则进行优先选择。
VectorDrawable（矢量图）-> WebP ->Png（有透明通道） -> jpg（无alpha通道）
```

###  删除 Java access 方法 
```text
可以参考  西瓜技术团队 的文章 “西瓜视频apk瘦身之 Java access 方法删除”

Java语言的封装性要求一个类的私有成员不能被其他类直接访问，
然而内部类和其外部类可以直接访问对方的私有成员，这个现象显然违反了Java封装性的要求，
因此为了能提供内部类和其外部类直接访问对方的私有成员的能力，又不违反封装性要求，
Java编译器在编译过程中自动生成package可见性的静态access$xxx方法，
并且在需要访问对方私有成员的地方改为调用对应的access方法

access方法的影响
1、dex文件格式对类、方法、字段的数目有限制，超过65535就得分包。
因此额外生成的access方法数的增加会导致app对multidex依赖的增强。
2、大量的access方法会使得代码体积增加，apk文件也会变大，在西瓜视频中未优化前access方法增加了几百k的包大小；
3、access方法调用会有额外开销（比如要分配栈帧），对性能也有一定的影响。

解决办法：
1、选择使用 ASM在编译时自动删除 access方法
2、ReDex 也提供了 access-marking 这个功能去除代码中的 Access 方法 。
```

### 代码编写方面的优化 
```text
每一个枚举值都是一个对象, 在使用它时会增加额外的内存消耗 ，同时也会增加 DEX文件的大小 。
所以 android官方文档建议尽量避免使用枚举。
```

### R文件常量内联，R文件瘦身  （建议）
```text
R.layout.activity_main 实际上对应的是一个 int 型的常量值，
那么如果我们编译打包时，将所有这些对 R 类的引用直接替换成常量值，
替换一个就会减少一个 R文件中的字段，R文件体积就会变小。
setContentView(213196283); 效果也是一样的，那么 R.java 就存在优化的空间。

R.java  类里有2种数据类型，一种是 static final int 类型的，这种常量在运行时是不会修改的，
另一种是 static final int[] 类型的，虽然它也是常量，但它是一个数组类型，
并不能直接删除替换，所以打包进 apk 的 R 文件中，
理论上除了 static final int[] 类型的字段，其他都可以全部删除掉。

一个工程如果有多个 模块，会对应多个R文件，
如果工程比较大，R文件也会比较大，甚至会有几个M。
R文件的 Field 过多时 可能会导致 MultiDex 65536 的问题。

可以使用地方的一些工具 快速实现R文件内联瘦身，例如 抖音的 ByteX
https://github.com/bytedance/ByteX
```

###  resConfigs 去除无用语言资源 （建议）
```text
例如使用了 AppCompat，如果不做任何配置的话，最终 APK 包中会包含 AppCompat 中所有已翻译语言字符串，
无论应用的其余部分是否翻译为同一语言。
对此，我们可以 通过 resConfig 来配置使用哪些语言，从而让构建工具移除指定语言之外的所有资源。
```



### 资源在线化
```text
一些图片等资源 可以放服务器，结合预加载技术，可以减少apk打包大小。
```

###  统一应用风格，减少 xml 配置文件 和图片 （建议）
```text
如设定统一的 字体、尺寸、颜色和按钮按压效果、分割线 shape、selector 背景等。
```

### 剪裁 so 库 （建议）
```text
如果用到第三方的库的某个功能，可以把不需要的部分剪裁掉，
减少 so 的体积，例如 ffmpeg 中 可以通过选择配置，编译出自己想要功能的so 即可。

过滤掉不常用 CPU 平台的 so 。 
abiFilters "armeabi" ，“xxx”
```

###  XZ Utils  压缩 so 
```text
Native Library 同 Dex 一样,也可以使用 XZ Utils 进行压缩，
对于 Native Library 的压缩，我们 只需要去加载启动过程相关的 Library，
而其它的都可以在应用首次启动时进行解压。
``` 

### So 动态下载
```text
可以参考 腾讯的技术文章 “动态下发 so 库在 Android APK 安装包瘦身方面的应用”
https://mp.weixin.qq.com/s/X58fK02imnNkvUMFt23OAg
```

###  插件化思路
```text
如果我们 App 当中的每一个功能都是一个插件，并且都是可以从服务器下发下来的，那 App 的包体积肯定会小很多。
```


# 电池耗电优化 (完成)
```text
手机耗电是通过使用相应的硬件模块来消耗电能。
CPU、屏幕、WIFI、数据网络、GPS、音视频通话在日常耗电量中占比最大。
```

## Android系统做的 省电优化
```text
1、Android 4.4 新增“硬件传感器批处理”支持
“硬件传感器批处理”(需要硬件支持)可以显著降低传感器持续活动所消耗的电量。
因为我们有时并不需要非常快的数据更新频率，比如GPS定位不需要一秒十几次位置更新。  
所以我们通过 API 可以指定系统向应用传输批量传感器事件的频率。
系统会将一段时间内发生的所有事件保存起来，然后一次性将它们传输到应用，
而不是在每个事件发生时将其传输到你的应用。
这样处理只降低了传输的频率并不会减少次数。

2、Android4.4 新增 AlarmManager “对齐唤醒”API支持，
简单的说就是每隔一段时间后台程序唤醒一次，比如你的QQ、微信在后台运行，如果你没有开启智能唤醒，
那么QQ有消息了后台马上唤醒，微信有消息了也马上唤醒，这样就会非常耗电。
对齐唤醒就是每隔一段时间唤醒一次，能省电不少。

3、Android 5.0 ，加入了 Volta 项目 ，
这个项目可以对电池进行更精细的管理，能够大大降低操作系统对于电池电量的持续消耗。

4、Android5.0 进入 任务调度机制 JobScheduler ，省电原理类似于 Android4.4加的 闹钟对齐唤醒。

5、Android6.0 加入 Doze 休眠低功耗模式 。
在 Doze 模式下，系统通过限制app访问网络和其对CPU使用来达到省电的目的，
app的网络访问功能会被禁用，同时延时执行作业，异步任务及标准闹钟事件。
为了保证app的工作，系统会周期地退出Doze模式，以保证app完成那些被延时的任务。

6、Android6.0 加入 App Standby（ app待命模式）。
这个概念则是针对于应用的，当App进入AppStandby状态后，也会受到网络等状态的限制。
此处不列出具体触发和退出条件 。

7、Android7.0 删除了三个隐式广播，减少无用功耗。

8、Android8.0 增加 “后台执行限制”。
每次在后台运行时，应用都会消耗一部分有限的设备资源，例如内存和CPU。
后台执行限制包括 后台服务限制和 广播限制。

9、Android8.0 增加 “后台位置限制”。
```



## 耗电分析工具
### Energy Profiler
```text
设备系统在 Android 8.0 以上的系统 使用 Energy Profiler
Androidstudio 中 view -> Tool Windows --> Profiler
其中会分栏显示 CPU 、 MEMORY 、NETWORK  、ENERGY
其中 ENERGY 就是电量的消耗情况。
```

### battery-historian 电量分析工具
android 8.0 以下使用 battery-historian 
```text
https://github.com/google/battery-historian 
在本地搭建 battery-historian 工具有两种方法
第一种是用 dock 方式，第二种是 编译编码方式。
具体步骤参考了 https://blog.csdn.net/cui130/article/details/103934363 
但是可能是因为网络的问题
走到 go get -d -u github.com/google/battery-historian/...  无法下载了...
go get -d -u github.com/google/battery-historian/...
go get github.com/google/battery-historian/...: module github.com/google/battery-historian: Get "https://proxy.golang.org/github.com/google/battery-historian/@v/list": dial tcp 172.217.161.145:443: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.

以后有空再用编译源码的方式去弄了，
暂时先用一个在线的服务
https://bathist.ef.lc/

// 获取手机电量报告信息
adb shell dumpsys batterystats --enable full-wake-history //允许记录电池日志
adb shell dumpsys batterystats --reset   // 清空日志

adb bugreport bugreport.zip    //  7.0 和更高版本的开发设备中获得 bug 报告
adb bugreport > bugreport.txt  // 6.0或更低版本的设备

将导出的文件上传到 工具里进行分析 https://bathist.ef.lc/
```

## 检查统计 单个App的耗电情况
```text
battery-historian 、Energy Profiler 中均可以根据包名 选择指定的APP进行分析。
```

## 省电优化方案
### 优化后台耗电
```text
避免后台长时间获取 WakeLock、WIFI 和蓝牙的扫描等。
WakeLock 是android系统中一种锁的机制，只要有进程持有这个锁，系统就无法进入休眠状态。
WakeLock 要记得释放。
```

### 制定 符合系统的耗电规则 
```text
1、Alarm Manager wakeup 不能唤醒过多：
当手机不在充电状态， wakeup 唤醒次数 每小时不能大于 10 次。

2、不能频繁使用局部唤醒锁，
当手机不在充电状态，partial wake lock 持有不能超过1小时。

3、后台网络使用量不能过高
当手机不在充电状态而且应用在后台，每小时网络使用量不要超过 50MB。

4、、后台 WiFi scans 不能过多
当手机不在充电状态而且应用在后台，每小时 WiFi scans 不能 大于4次。
```

### CPU 时间片优化
```text
Android 手机包括 AP 和 BP 两个 CPU。
AP 即 Application Processor，所有的用户界面以及 App 都是运行在 AP 上的。
BP 即 Baseband Processor，手机射频都是运行在这个 CPU 上的。
我们所说的耗电，PowerProfile 文件里面的 CPU，指的是 AP。

CPU 耗电通常有两种情况：
1、长期频繁唤醒。
原本可以仅仅在 BP 上运行， 但是因为唤醒，AP 就会运作 增加耗电量。

2、CPU 长期高负荷。
例如 App 退到后台的时候没有停止动画，或者程序有不退出的死循环等等，导致 CPU 满频、满核地跑。

3、一些计算不够优化
例如浮点运算比整数运算更消耗 CPU 时间片，耗电也会增加。
一些图像算法比较耗时，可以换个更优的算法也可以大大降低cpu使用。

所以常用优化 CPU 时间片的方式有：
1、定位并解决 CPU 占用率异常方法。
2、减少应用在后台的唤醒和运行。
```

### 网络耗电优化
```text
通常情况下，使用 WIFI 连接网络时的功耗要低于使用移动网络的功耗。
使用移动网络传输数据，电量的消耗有以下3种状态：
Full power ：高功率状态，移动网络连接被激活，允许设备以最大的传输速率进行操作。
Low power ：低功耗状态，对电量的消耗差不多是 Full power 状态下的 50%。
Standby ：空闲态，没有数据连接需要传输，电量消耗最少。

所以为了避免网络连接所带来的电量消耗，我们可以采用如下几种方案：
1、尽量在 WIFI 环境下进行数据传输，在使用 WIFI 传输数据时，
应该尽可能增大每个包的大小（不超过最大传输单元 MTU ），并降低发包的频率。

2、在蜂窝移动网络下需要对请求时机及次数控制，
可以延迟执行的网络请求稍后一起发送，最好做到批量执行，尽量避免频繁的间隔网络请求，
以尽量多地保持在 Standby 状态。

3、使用 JSON 和 Protobuf 进行数据压缩，减少网络使用时间。

4、禁止使用轮询功能：轮询会导致网络请求一直处于被激活的状态，耗电过高。
```

### 定位耗电优化 、传感器耗电优化 、蓝牙耗电优化等
```text
1、根据场景谨慎选择定位模式。
对定位准确度没那么高的场景可以选择低精度模式。

2、可以考虑网络定位代替 GPS。

3、使用后务必及时关闭，减少更新频率，
例如定位开启一定时间后超过某个阈值可以执行一个兜底策略：强制关闭 GPS。
```

### 界面耗电优化
```text
1、离开界面后停止相关活动，例如关闭动画。

2、耗电操作判断前后台，如果是后台则不执行相关操作。
```






# 稳定性 优化 ( 完成 )

## 崩溃率 、DAU
```text
DAU ( Daily Active User )，日活跃用户数量。
崩溃率 是奔溃次数或人数除以 DAU 。
```



##  稳定的纬度 
```text
稳定的纬度：
1、崩溃角度
非常重要，出现闪退现象会导致用户体验非常差。
容错机制，发生异常尽量不要闪退，而是给出提示。
不要随意使用try catch去隐藏问题：而应该从源头 了解崩溃的本质原因，保证后面的运行流程。
 
2、性能稳定
启动速度、流畅卡顿程度、内存占用、耗电量、界面绘制速度、网络流量、 
保证业务功能可用。ANR 响应超时、 能持续运行多长时间

3、业务可用性纬度。
保证APP主流程 和核心路径的稳定可用性。确保核心业务高可用。因为核心业务覆盖的人群最多。
其他方面只有经常用的人才有可能触发。
保证业务可用的高效可用，性能不能相差起伏太大。
```

## 稳定优化的方向
```text
解决方案：
1、预防为主、监控修复为辅
①、开发阶段 ，采用成熟稳定方案、加强编码能力；
对APP的 cpu、内存、进程线程数、网络流量、耗电量、log记录、启动页耗时 等信息记录和分析

②测试阶段：加强机型覆盖测试、压力测试、特殊场景测试(例如无网络、故意下发错乱数据等)；
从用户角度入手，尽量模拟真实使用场景、测试验证。不要从开发者角度进行测试。
线下大规模覆盖测试、发现问题 解决问题；

③发布阶段：进行灰度发布、多轮发布 ；
发布前多测试、捕获异常上传并分析 、发新包修复或者热修复。 
如果新发布的功能出现BUG，可以通过远程开关进行关闭功能模块，不让该功能显示。
 
④ 运维阶段： 版本回退策略、热修复、发布新版本
线上定位问题 定位机型。
发生崩溃异常时，需要远程上报相关信息，包括 报错信息、机型、系统版本、APP版本、渠道等。
```


## 稳定监控工具
```text
1、例如Android 自带的 monkey 
2、 腾讯的 Bugly 
3、阿里的  mobileperf  
```

## APP单元测试
### 单元测试
```text
// 黑盒测试
一般而言，都是自己或者测试做做黑盒测试，模仿用户使用场景 使用即可。
我问过很多人，他们基本都是这样测试后就上线了。

// 白盒测试
1、对于一些 要求严谨的大型 app ，例如 微信 支付宝，肯定是要做白盒测试了。
黑盒测试无法完全覆盖，一旦出错，损失无法承受 ，当然，这种级别的测试，都有专门的测试人员。

2、有一些大型的app ,编译时间比较长，或者路径比较深。采用手动点击试验看效果的方法，会比较浪费时间。
为了免去编译安装的等待时间，要引入白盒测试，缩短自测的时间。

// TDD ( Test-Driven Development ) 测试驱动开发 
```

###  单元测试 JUnit4 方案
Androidstudio 自带。
```text
测试运行在本地开发环境的Java虚拟机上，无需连接Android设备或模拟器。
因此，无法获得Android相关的API，只能测试只使用Java API的一些功能。

@Test	表示此方法为测试方法
@Before	在每个测试方法前执行，可做初始化操作
@After	在每个测试方法后执行，可做释放资源操作
@Ignore	忽略的测试方法

assertEquals	断言传入的预期值与实际值是相等的
assertArrayEquals	断言传入的预期数组与实际数组是相等的
assertNull	断言传入的对象是为空
assertTrue	断言条件为真
assertSame	断言两个对象引用同一个对象，相当于“==”
assertThat	断言实际值是否满足指定的条件

@Ignore("等会再测")
@Test
public void getAppVersion(){  }

@Test
public void valueAdd() {
    MyDeviceTool tool = new MyDeviceTool() ;
    int sum = tool.valueAdd(10 ,5);
    assertEquals(5 ,sum);
}
```

### 单元测试 Instrumentation 方案
```text
需要将整个项目打包成apk，上传到模拟器或真机上，就跟运行了一次app 。
最近的文章(20201222)讲到 Androidstudio +Instrumentation 比较少，
Androidstudio3.4.1 默认生成的代码中也没有 Instrumentation 相关配置，
是被替代了吗，暂时去看看其他的方案。
```

### 单元测试 robolectric 方案
With Robolectric, your tests run in a simulated Android environment inside a JVM, 
without the overhead of an emulator.
```text
http://robolectric.org
https://github.com/robolectric/robolectric

// 配置
testImplementation 'junit:junit:4.12'
testImplementation 'org.robolectric:robolectric:4.1'
testImplementation 'org.robolectric:shadows-multidex:4.1'

testOptions {
    unitTests {
        includeAndroidResources = true
    }
}
    
// 会提示下载文件失败，你可以手动下载一下。
Downloading: org/robolectric/android-all/9-robolectric-4913185-2/android-all-9-robolectric-4913185-2.jar 
from repository sonatype at https://oss.sonatype.org/content/groups/public/
Transferring 118099K from sonatype

下载地址就是按提示拼接一下,将下载好的文件放到 android-all-9-robolectric-4913185-2.jar.tmp 目录下 ，
用 everything 全局搜索一下。
---->
https://oss.sonatype.org/content/groups/public/org/robolectric/android-all/9-robolectric-4913185-2/android-all-9-robolectric-4913185-2.jar

// 简单示范
@RunWith(RobolectricTestRunner.class)
@Config( sdk = 28)
public class TestShow {
    @Test
    public void myTest(){
        Context context =  RuntimeEnvironment.application.getApplicationContext() ;
        MyDeviceTool myDeviceTool =  new MyDeviceTool();
        String packageName =myDeviceTool.getPackageName(context) ;
        WkLogTool.showLog("packageName="+packageName);
    }
}

我用的较少，一般只是用来测试一下逻辑。
```
 
 
## crash 崩溃解决办法
```text
1、(线上) 常规Crash
发版时或者通过热修复解决

2、系统级 Crash 尝试Hook绕过

3、疑难 Crash重点突破或更换方案
目前技术能力暂时无法解决的就想办法规避问题，更换实现方案
```
 
## 如果发生了异常情况，怎么止损
```text
1、有异常情况，紧急关闭功能的入口，让App处于可控的状态。
2、热修复。
3、兜底策略，如果监测到连续多次在同一个地方奔溃，可以拦截此流程，
给予用户相关提示，不要一直崩溃。然后再上报错误。
``` 
 
 
 
# 代码质量 控制 (完成)
## 导致 代码质量问题 的原因
```text
1、新手 ，经验和能力不足
2、人员的调动和更换，导致对业务的理解有偏差
3、没有一个约定的 代码规范 ，导致沟通和维护困难
```

## 如何保证代码质量
```text
1、架构师搭建、设置 合理优秀的基础框架。
2、要求 开发者 单元测试、代码自测。bug率跟绩效挂钩。

3、代码 评审、复查、 review 
代码提交后，由其他人 一般是水平更高的人来评审代码，
评审点可以放在 代码是否符合约定的代码规范、 设计思路和逻辑、性能 ，
必要时要进行重构，实行老人带动新人成长 
一般而言， 采用 以小组为单位，组长责任制的代码复查形式。
小组长不要分配太多的开发任务，而是以指导设计、评审代码为主。

4、定期开总结大会，做分享，团队成员能力共同提高 
```

# 技术选型 (完成)
```text
首先要确保一点：技术选型是稳定压倒一切、先验证后使用。
不稳定的技术或框架，在后期会带来很多的麻烦，

此项技术是否要被采纳，可以考虑以下因素
1、该技术是否成熟，是否已经被广泛应用和验证过。
一些技术随着开发版本的推进，API会大幅改变，甚至不再维护旧版本，导致旧版本中的BUG无人修复，逼迫你升级。
新技术可能会有很多未知的BUG，如果你深度使用后遇到一个Bug，你自己无法解决 也没人解决过，你会非常难受。
还有一些新技术可能会影响你的APP上架。

2、该技术是谁发起和主持的，权威性如何，后续更新如何，是否有版权风险。
可能会有一些技术精英会开源一个框架，但是并没有形成一个好的社区来维护，后期可能导致项目更新缓慢，甚至停更。
有些可能会慢慢地演化成开源版和收费版，你想要的功能只在收费版中提供。影响你的预算。

3、该技术的复杂性如何、学习成本 、以后替换的成本如何。
在一些工期比较紧的项目中，如果采用新技术，因为团队成员需要学习成本，可能无法把控开发进度。

4、使用该技术是否带来过多的冗余模块，对程序的健壮性是否有影响。
例如一些工具类想做得大且全，但是你引入它只是为了使用它的一个小模块，此时要好好考虑和验证下。
如果出现 依赖冲突、安装包增大等问题，你又对该技术不太熟悉，无法进行优化和排查，
会给项目带来很多不稳定因素。

5、对技术进行考虑时不仅仅要看到它的优点，更要看到它的不足点，改技术在宣传时，它一般是对缺点避而不谈的，
所以你要留心并验证。
```


# 提问
## 如何 提高 线上代码 质量
```text
1、上线前先做好本地测试，加入 bugly等获取异常信息，以便排查修复
2、支持热更新 ，无感知修复小bug
3、全局异常捕捉，防止APP崩溃。
```




 
## 如何自定义 Lint
```text
Android Lint 是 ADT 16 引入的一个代码扫描工具 ，
通过对代码进行静态分析，可以帮助开发者发现代码质量问题和提出一些改进建议。
Lint 已经被集成在 AS 中。
原生 Lint 有时无法满足自己的需求，例如团队的编码规范。所以有时需要进行自定义 Lint 。

自定义lint 的方案目前主要有两种。
1、第一种当然是 谷歌官方的，https://sites.google.com/a/android.com/tools/tips/lint-custom-rules 
不过这种方案有点不妥，这种方案配置的 lint 规则会 影响机器上所有的Android工程。
拷贝到了 .android/lint 文件夹下了。所以一般采用第二种方案。

2、第二种方案是 LinkedIn (就是那个领英) 的，https://engineering.linkedin.com/android/writing-custom-lint-checks-gradle
将lint.jar放到一个 aar 中。将需要 lint 检测的项目中引入这个 aar，仅对当前工程有效。
```

###  demo ( LinkedIn 方案) 
```text
参考博客写的demo ，以 aar方式引用出了点问题。(只生效一次，暂时不知道原因)
暂时只用 java lib 的方式 引用。
具体的使用请参考代码 https://gitee.com/hnyer/aivin_lint_demo 

目前有的效果有 
枚举检测、图片大小检测、系统日志检测、命名规范检测、xml 字段名命名检测
手动创建线程检测、系统吐司检测、xml硬编码检测、代码中硬编码检测
```

![](../pics/自定义lint.png)