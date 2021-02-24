# 性能优化


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

## 内存优化
```text
1、别频繁GC
2、控制好对象的引用，防止对象一直无法释放。
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


# 卡顿优化
```text

```


# 绘制优化
```text

```


# 包体积优化
```text

```


# 电池耗电优化
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

## 检查统计 单个App的耗电情况
```text
com.wk.myapplication
 

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

# 内存泄漏
## Handler 内存泄漏
请查看 Handle 详解 这一块。

## AsyncTask 内存泄露
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


# 稳定性 优化
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

监控工具：
1、例如Android 自带的 monkey 
2、 腾讯的 Bugly 
3、阿里的  mobileperf  
```

## 单元测试
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

##  单元测试 JUnit4 方案
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

## 单元测试 Instrumentation 方案
```text
需要将整个项目打包成apk，上传到模拟器或真机上，就跟运行了一次app 。
最近的文章(20201222)讲到 Androidstudio +Instrumentation 比较少，
Androidstudio3.4.1 默认生成的代码中也没有 Instrumentation 相关配置，
是被替代了吗，暂时去看看其他的方案。
```

## 单元测试 robolectric 方案
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
 
 
 
 
# 代码质量 控制
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

# 技术选型
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

# 如何 提高 线上代码 质量
```text
1、上线前先做好本地测试，加入 bugly等获取异常信息，以便排查修复
2、支持热更新 ，无感知修复小bug
3、全局异常捕捉，防止APP崩溃。
```



# 资源去重 
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

# 自定义 Lint
```text
Android Lint 是 ADT 16 引入的一个代码扫描工具 ，
通过对代码进行静态分析，可以帮助开发者发现代码质量问题和提出一些改进建议。
Lint 已经被集成在 AS 中。
原生 Lint 有时无法满足自己的需求，例如团队的编码规范。所以有时需要进行自定义 Lint 。

自定义 lint 的方案目前主要有两种。
1、第一种当然是 谷歌官方的，https://sites.google.com/a/android.com/tools/tips/lint-custom-rules 
不过这种方案有点不妥，这种方案配置的 lint 规则会 影响机器上所有的Android工程。
拷贝到了 .android/lint 文件夹下了。所以一般采用第二种方案。

2、第二种方案是 LinkedIn (就是那个领英) 的，https://engineering.linkedin.com/android/writing-custom-lint-checks-gradle
将lint.jar放到一个 aar 中。将需要 lint 检测的项目中引入这个 aar，仅对当前工程有效。
```

##  demo ( LinkedIn 方案) 
```text
参考博客写的demo ，以 aar方式引用出了点问题。(只生效一次，暂时不知道原因)
暂时只用 java lib 的方式 引用。
具体的使用请参考代码 https://gitee.com/hnyer/aivin_lint_demo 

目前有的效果有 
枚举检测、图片大小检测、系统日志检测、命名规范检测、xml 字段名命名检测
手动创建线程检测、系统吐司检测、xml硬编码检测、代码中硬编码检测
```

![](../pics/自定义lint.png)