本博客不保证完整性和正确性,请自行甄别。 善用左上角的搜索功能。  <br>
托管地址 https://gitee.com/hnyer/androidDev

| 联系我  |
| --- |
| email：aivin666@qq.com 、wechat: Aivin666666|
|https://blog.csdn.net/dengpanwen  //CSDN |
|https://hnyer.gitee.io  //红尘之声 |
|http://hnyer.gitee.io/resume //在线简历 |




# Java基础
- [x] JVM 架构
- [x] Java 内存模型、内存结构、Java的内存分区
- [x] JVM 垃圾回收机制、老年代有什么算法
- [x] 说一下 Android 用的是哪几种 垃圾回收算法
- [x] 方法区有什么东西 
- [x] 说说你对类加载机制的了解？类什么时候会被加载？ 类加载的过程具体生命周期是怎样的？
- [x] DexClassLoader 与 PathClassLoader 的区别
- [x] 类加载的过程，属性先加载还是方法先加载
- [ ] 对象创建过程
- [x] 讲下Java的 双亲委派
- [x] 拉圾回收的 GCRoot 是什么
- [x] GCRoot 的类型 ,举例说明。比如说Activity和它的匿名内部类Handler，分析下引用链，对应的 gcroot 是哪个
- [x] 讲下 equals 和 hashcode ，他们为何必须一起重写
- [ ] 成员变量和局部变量的区别。为何成员变量需要jvm在对象初始话过程中赋默认值？
- [ ] class文件的组成？常量池里面有什么内容？
- [x] java 和 字节码 有什么区别
- [x] 编译期注解处理的是字节码还是java文件
- [x] 字节码注入 、字节码插桩 、字节码注入
- [ ] 自动装箱发生在什么时候？编译期还是运行期
- [ ] 内部类访问外部类
- [x] java有什么特性，继承有什么用处，多态有什么用处
- [x] java 里带$的函数 是什么意思
- [x] 硬件加速 是什么原理
- [x] 进程优先级
- [x] 静态方法，静态对象为什么不能继承 （此问题错误，可以被继承，不能被重写）
- [x] JMM 可见性 、原子性 、有序性 、synchronized 可以保证什么
- [ ] 线程间同步的方法
- [x] 重入锁 ReentrantLock 、synchronized 和 读写锁 ReadWriteLock 的区别 
- [x] Lock 接口 的实现，以及与 synchronized 的区别
- [ ] 平常有用到什么锁，锁之间的区别 ,synchronized 底层原理是什么
- [x] synchronized 是公平锁还是非公平锁 , ReteranLock 是公平锁吗？ 是怎么实现的
- [x] synchronized 跟 ReentranLock 有什么区别？ 发生异常的场景
- [ ] synchronized 和 volatile 的区别？为何不用 volatile 替代 synchronized？
- [ ] 说说你对 volatile 字段有什么用途？
- [ ] Synchronize 关键字后面跟类或者对象有什么不同
- [x] 类锁和对象锁互斥么
- [x] CAS 原理
- [x] AQS 了解吗
- [x] 泛型是什么、泛型有什么优点
- [x] 泛型通配符
- [x] 泛型擦除 是什么，为何会有擦除 、擦除的时机 
- [ ] 泛型是怎么解析的，比如在 retrofit 中的泛型是怎么解析的
- [x] 泛型 为何会有 协变 和 逆变，PECS 规则
- [x] string 比较 equals 和 == 有什么区别
- [x] 函数式编程 如何理解
- [x] 说说你对 注解 的了解，是怎么解析的
- [ ] 编译时注解 与 运行时注解，为什么retrofit要使用运行时注解？什么时候用运行时注解？
- [x] 反射 是什么，在哪里用到，怎么利用反射创建一个对象
- [x] 反射可以反射final修饰的字段吗？

# 框架源码
- [x] 有看过哪些框架的源码 (leakCanary / RecyclerView)
- [x] 讲下 leakCanary 原理 
- [x] leakCanary 为什么不用 虚引用？ 引用队列里面存的是什么？

# android 基础
- [x] Android APP的 入口函数 在哪里 
- [x] activity 启动模式
- [x] APP主页一般用哪种 启动模式 ，原因是什么
- [x] Activity 生命周期有哪些，知道 onRestart 么； A启动B ，A的 onStop 一定会执行吗；按 Home 按键后的生命周期
- [x] activity 启动另外一个activity的时候横屏切换再点击返回，过程中发生的生命周期
- [x] Activity 与 AppCompatActivity 区别
- [x] Fragment#onHiddenChanged 是生命周期方法么？如何触发？
- [ ] Fragment hide show生命周期变化
- [ ] Fragment replace生命周期变化
- [ ] ViewPager切换Fragment什么最耗时？
- [ ] fragment 的 replace 和end？ 的区别
- [ ] Activity和Fragment的通信方式；系统为何会设计Fragment#setArgument方法。
- [x] Service的生命周期，两种启动方法，有什么区别
- [ ] 如何在 Service 中打开一个 Activity  
- [x] 有什么工具可以看到 Activity栈信息么？多个栈话，有方法分别得到各个栈的Activity列表么
- [x] ANR 是怎么回事？怎么查？Service会引起 ANR 么 
- [x] 为什么 Activity.finish() 之后 10s 才 onDestroy 
- [x] SQLite 的数据库升级 
- [x] SharedPreferences 原理 ，读取xml是在哪个线程
- [x] SharedPreferences 的 apply 和 commit 有什么区别
- [x] SharedPreferences 可以跨进程通信吗？ 如何改造成可以跨进程通信的?   
- [ ] ContentProvider具体实现。
- [ ] binderService方法中的回调具体运行在哪个线程？binder线程池最大线程数是多少？自定义的Callback远程调用，运行在哪个线程？为何不是主线程，如果运行在主线程会有哪些问题？
- [x] 悬浮窗如何实现
- [ ] ANR的log中关键字是什么
- [ ] Activity的onSaveInstance方法何时调用？它跟onPause、onStop的调用顺序如何？
- [ ] 方法内部的匿名内部类，比如说给View设置的OnClickListener，它里面相关调用外部方法的形参，必须使用final修饰这个形参，为何？
- [ ] Android里面进程间通信方式，ContentProvider可以用file实现么？
- [ ] Activity启动模式，以及各启动模式生命周期问题
- [x] Activity怎么启动 Service 、Activity 与 Service 交互，Service 与 Thread 的区别
- [x] ANR 了解过吗？有没有实际的ANR定位问题的经历
- [x] AndroidManifest.xml 中 application 中配置 largeHeap ，真的能分配到大内存吗？
- [ ] ThreadLocal 实现
- [ ] 通知的类别
- [x] 全局广播与局部广播区别 
- [x] 本地广播 LocalBroadcastManager 实现 ,为何效率高 ?
- [ ] 广播与 RxBus 的区别
- [ ] Rxjava 是怎么实现线程切换的
- [ ] Rxjava 自定义操作符
- [ ] RXJava 怎么切换线程
- [ ] Bugly 日志收集的原理是什么？
- [x] 做过一些 SDK 的操作吗？
- [x] 为什么会有 R文件映射表 ？直接使用资源的路径不好么？
- [x] dex 文件结构了解过么？为何会有 65535 的限制 
- [ ] mutildex 技术了解么？这项技术的目的是什么？
- [x] Parcelable 和 Serializable 本质区别 。说原理 ,不要说用法
- [ ] Bundle 是什么数据结构 ? 利用什么传递数据
- [x] 两个 getDrawable 取得的对象，有什么区别 ？
- [x] onSaveInstanceState() 、onRestoreInstanceState() 平时是怎么用的 , 什么时候有值，什么时候为空
- [ ] Scroller 有什么方法，怎么使用的
- [ ] canvas lock的缓冲区是怎么回事

# Android 动画
- [x] 介绍一下 Android 动画
- [x] 补间动画与属性动画的区别，哪个效率更高
- [x] 动画里面用到了什么设计模式
- [x] 属性动画更新时会回调 onDraw 吗
- [x] 动画连续调用 的原理是什么




# 线程 、进程 、跨平台
- [x] 多线程如何实现 ,有哪些方式 、四种线程池原理 ,线程池的类型 
- [x] 线程池如何配置，核心线程数你一般给多少 、线程池的参数 ,最大线程数和核心线程数的关系 
- [x] 线程池 task 的优先级如何实现？（优先级队列 PriorityBlockingQueue ）
- [x] 线程池 拒绝策略有几种 
- [x] 如何禁止 手动创建线程 。使用 自定义 lint 
- [x] 阿里编程规范 不建议使用 Executors 来创建线程池，为什么
- [x] IdleHandler 调用时机
- [x] IdleHandler用过吗 ？IdleHandler 应用场景
- [ ] Handler休眠是怎样的？epoll 的原理是什么？如何实现延时消息，如果移除一个延时消息会解除休眠吗？
- [x] Handler内存泄漏 的GCRoot是什么 ？
- [x] 说说你对Handler机制的了解，同步消息，异步消息等
- [x] Handler机制了解吗？一个线程有几个Looper？为什么？
- [x] 简单描述下Handler,Handler是怎么切换线程的
- [x] Handler 同步屏障
- [ ] Launcher启动App的流程，中间有几种跨进程通信(socket)
- [ ] 源码中有哪里用到了 AtomicInteger
- [x] 跨进程通信了解多少？管道了解吗？
- [x] 描述一次跨进程通讯
- [x] 说说 binder 机制的原理
- [x] binder 的 mmap()
- [x] android 跨进程通信了解吗？ 共享内存用过吗？binder驱动了解吗？
- [ ] binder 怎么验证 pid ? 
- [ ] binder 进程间通信可以调用原进程方法吗 ？
- [ ] 生命周期都是通过什么调用的？有用过AIDL吗？
- [x] AIDL in out oneWay 代表什么意思
- [x] 你们用的什么消息通信机制
- [x] 多进程 怎么实现？如果启动一个多进程APP，会有几个进程运行？
- [x] 怎么终止一个线程，thread.interrupt() 一定有效吗




# 启动流程 、加载流程
- [ ] 从桌面点击icon图标开始，整个启动activity的启动过程
- [ ] ContentProvider启动流程
- [ ] Application 启动流程
- [ ] Application 中持有 静态的信息，有何缺点 ？如何改进 
- [x] Application 中可以显示Dialog么 ？为什么？
- [x] 使用 ActivityLifecycleCallbacks 做了哪些事情？
- [ ] 说说App的启动过程 , 在ActivityThread的main方法里面做了什么事，什么时候启动第一个Activity？
- [ ] AMS 交互调用生命周期是顺序的吗
- [x] Activity、Window 、PhoneWindow、DecorView、View、ViewRootImpl 的关系 
- [x] activity 有几个 Window
- [x] 除了 Activity 还有别的方式显示 Window 出来么 
- [x] DecorView 的布局是什么样的 
- [x] DecorView 的创建时机
- [x] ViewRootImpl 创建的时机 
- [x] PhoneWindow 是在哪里初始化的
- [x] setContentView 是如何把xml文件 化成View并显示到 Activity中的 
- [x] LayoutInflater 是如何把xml布局文件转换成View对象的（反射）？View树如何生成的？怎么优化？
- [x] RecyclerView 的缓存结构是怎样的？缓存的是什么？cachedView 会执行 onBindView 吗?
- [x] RecyclerView 嵌套 RecyclerView，NestScrollView 嵌套 ScrollView 滑动冲突
- [x] 讲一下 RecyclerView 的缓存机制,滑动10个，再滑回去，会有几个执行 onBindView
- [x] 如何实现 RecyclerView 的局部更新，用过 payload 吗, notifyItemChange 方法中的参数？
- [x] RecyclerView 和 ListView 区别、特点、缓存 、懒加载、局部刷新 ,多种type场景下怎么避免滑动卡顿 ,怎么优化滑动
- [x] RecyclerView 缓存结构，RecyclerView预取，RecyclerView局部刷新
- [x] RecyclerView 里面那个 changedSrape, attachedScrape 之类的到底是在做什么 ? 
- [x] RecyclerView 的 LayoutManager 有哪几种
- [x] 想改变 ListView 的高度，怎么做
- [ ] Launcher 启动图标，有几个进程？
- [ ] Jetpack 组件用过哪些？
- [ ] LifeCycle 的原理是什么？如果在onStart里面订阅，会回调onCreate吗？
- [ ] Activity内 LinearLayout 红色 wrap_content ,包含View绿色 wrap_content ,求界面颜色
- [ ] ViewPager 切换掉帧 有什么处理经验？
- [ ] ViewPager2 原理
- [ ] setFactory 和 setFactory2 有什么区别？
- [ ] 如何自定义实现一个 FlexLayout
- [ ] A Activity打开B Activity的生命周期变化，会有什么方法打断吗？
- [ ] 登陆功能，登陆成功然后跳转到一个新Activity，中间涉及什么？从事件传递，网络请求,AMS交互角度分析

# UI 绘制 
- [x] View 绘制流程
- [x] onMeasure 、onLayout 、onDraw关系
- [x] 自定义 View 的几种方式。onMeasure、onLayout、onDraw 方法都何时需要重写 
- [x] MeasureSpec 讲一下
- [x] 讲下 onMeasure方法 , 如何测量 ，测量模式 ，入参为什么是int类型 
- [ ] 为什么会多次调用 onMeasure 和 onLayout 方法
- [x] 怎么获取view的宽高，如何确定值是准确的 ,有遇到是0的情况吗
- [x] 为什么 view.post 可以获得宽高，有看过view.post的源码吗 
- [x] 自定义 LinearLayout ，怎么测量子 View宽高
- [ ] 在 View 绘制中 ，一个View的宽高由哪些因素决定？ 在 activit 中要拿到 View 准确的宽高有哪些方式
- [x] onCreate 、onResume 、onStart里面，什么地方可以获得宽高
- [x] 如何触发重新绘制
- [x] invalidate 每次都会触发 onDraw 么？invalidate 每次会触发 View#onLayout 么？
- [ ] requestLayout 和 invalidate 的流程
- [ ] requestLayout 和 invalidate 的区别
- [ ] ACTION_CANCEL 什么时候触发，触摸button然后滑动到外部抬起会触发点击事件吗，在+ + 滑动回去抬起会么
- [ ] 获取TextView的行数时，StaticLayout原理。
- [ ] 绘制的数据是如何提交到远端的SurfaceFlinger
- [ ] requestLayout调用后，都会调用哪些方法？
- [ ] 手势操作 ActionCancel 后怎么取消
- [ ] 说说你对屏幕刷新机制的了解，双重缓冲，三重缓冲，黄油模型
- [x] onAttachedToWindow 什么时候调用？

# 事件分发 
- [ ] 伪代码实现一个长按事件
- [ ] 首次 View 的绘制流程是在什么时候触发的
- [ ] MotionEvent#offsetLocation事件转发
- [ ] 滑动冲突如何解决？有几种方式？具体从哪个事件开始拦截？在哪里拦截？比如双层ViewPager嵌套的滑动冲突如何解决。
- [ ] 讲下事件传递 ，总体流程，DOWN事件拦截后，后续事件如何处理？，dispatchTouchEvent方法返回true后事件如何处理？
- [ ] setOnTouchListener,onClickeListener 和 onTouchEvent 的关系
- [ ] dispatchTouchEvent , onInterceptEvent , onTouchEvent 顺序，关系
- [ ] 说说 事件分发机制，怎么写一个不能滑动的 ViewPager
- [ ] RecyclerView 是怎么处理内部ViewClick冲突的
- [ ] ViewGroup在Action_Move时onIntercept返回true，事件怎么传递
- [ ] ViewPager中嵌套ViewPager怎么处理滑动冲突
- [ ] 怎么处理嵌套View的滑动冲突问题
- [ ] View的分发机制，滑动冲突
- [ ] 说一下 touch 触摸时间的传递流程图
- [ ] 如果onInterceptTouchEvent返回true，但是onTouchEvent返回了false，是什么效果？如果还想让其他View接收事件，该怎么做？



# 自定义控件
- [x] 自定义圆角图片
- [x] 自定义实现一个九宫格 如何实现



# 插件化 、热修复 (已过时，已废弃)
- [ ] 说说插件化的原理，资源的插件化id重复如何解决？
- [ ] 插件化 的原理是怎样的？
- [ ] 插件化的原理是什么？有没有什么非运行时插件化的解决方案？
- [ ] 插件化换肤方案
- [ ] 插件化的原理，startActivity hook了哪个方法
- [ ] 插件化的主要优点和缺点是什么？
- [ ] 插件化的原理，以及hook点，大概有两个
- [ ] 说说热修复的原理？
- [ ] 热修复的原理，资源的热修复的原理,会不会有资源冲突的问题
- [ ] tinker的原理是什么,还用过什么热修复框架，robust的原理是什么？
- [ ] 热修复，主要说了ClassLoader的方式和Rubost的插装方式，重点介绍了Rubost的原理。


# 组件化 模块化
- [x] 多模块开发，不同的人可能会引入重复资源。相同的字符串，相同的icon等但是文件名并不一样，怎样去重
- [x] 组件化有详细了解过吗？ 
- [x] 组件化的实现方案 (参考 ARouter)
- [x] ARouter 路由原理？ 
- [x] 注解处理器是处理 java 还是字节码  (java文件)
- [x] ARouter 怎么实现接口调用 (IProvider)
- [x] ARouter 怎么实现页面拦截 (IInterceptor)
- [x] 如果不用 ARouter ，你会怎么去解藕。接口？设计接口有什么需要注意的？



# 网络相关
- [x] TCP 和 UDP 区别，TCP为何是三次握手，为何是四次挥手
- [x] 说一下 tcp 的建立连结和结束连结的通信过程
- [x] MediaPlayer 能同时播放多个音频么？ 如果需要播放多个提示音，如何实现
- [ ] 如何在网络框架里直接避免内存泄漏，不需要在 presenter 中释放订阅
- [x] http 与 https 有什么区别 
- [x] http1.0 、http1.1 、http1.2 、 HTTP3.0 有什么区别
- [ ] OkHttp 里面用到了什么设计模式？
- [ ] OkHttp连接池是怎么实现的？里面怎么处理SSL？
- [ ] OkHttp网络拦截器，应用拦截器 ? OKHttp有哪些拦截器，分别起什么作用
- [ ] OkHttp怎么实现连接池
- [ ] OkHttp 连接池的复用机制 
- [ ] 网络封装怎么实现？
- [ ] 如何上传数据？请求头关键字段和请求体格式
- [ ] 实现一个下载功能的接口
- [x] 平常抓包用什么工具
- [x] 在项目中有直接使用tcp 、socket 来发送消息吗
- [x] get 、 post 有什么区别


# NDK JNI
- [x]  java 中 int 类型对应的本地类型是什么
- [x]  JNI 中静态注册和动态注册的区别


# 算法
- [x] 了解哪些算法
- [x] 手写生产者消息者模型
- [x] 斐波那契台阶
- [x] 如何让三个线程按照顺序依次打印 1-100
- [x] 如何让两个线程循环交替打印
- [x] 删除数组中的重复元素
- [x] 反转数组
- [x] 实现一个快速排序
- [ ] 一个大致有序的数组如何排序，最快时间复杂度
- [ ] 不同面值的几个硬币，怎么求满足条件的最小值
- [ ] 实现一个双向链表
- [ ] K个一组反转链表
- [ ] 链表求和
- [ ] 二叉树最长结点集合
- [ ] 二叉树的最大深度
- [ ] 二叉树的每一层最左边节点
- [x] 有用过什么加密算法？AES , RAS什么原理？
- [x] 非对称加密 、对称加密
- [x] appkey 、 token 放在本地如何保存？ 如何加密比较好
- [ ] HashMap 讲一下，数据结构、hash过程、扩容、加载因子为何是0.75等。
- [ ] hashmap。hash冲突时给链表插入数据，1.7头插法，1.8尾插法。
- [ ] SparseArray 和 ArrayMap 各自的数据结构，前者的查找是怎么实现的，与HashMap的区别
- [ ] 说说HashMap的原理 HashMap查找的时间复杂度是多少？
- [ ] 讲下arraylist、hashmap、linkedlist、linkedhashmap的实现。linkedhashmap为何会有这样的特性(lru)?它有个参数，表示命中率和使用次数。
- [ ] lru是通过 linkedhashmap实现的么？
- [ ] 讲讲 LinkedHashMap 的数据结构
- [ ] ConcurrentHashMap  锁分段技术
- [ ] ArrayMap 和 SparseArray 的区别，实现。
- [ ] 如何求当前 Activity View 的深度

# 优化 、稳定性 、技术选型
- [x] Android 中如何查看一个对象的回收情况
- [x] 使用 Application. onTrimMemory( ) 优化
- [ ] 启动优化做过什么工作？ 如果首页就要用到的初始化 
- [ ] 你的APP冷启动时间是多少，普通页面打开时间是多少，举例几个认为比较好的优化方法
- [ ] WebView 性能优化做过什么工作
- [ ] 有没有做过什么 WebView 秒开的一些优化
- [x] 界面优化的一些方法，ConstraintLayout 实现三等分, ConstraintLayout 动画
- [x] 包体积的极致优化
- [x] 有什么实际解决UI卡顿优化的经历
- [x] 有做过什么Bitmap优化的实际经验
- [x] Apk的大小如何压缩
- [ ] 看视频的时候网络请求很慢怎么优化 ？
- [x] 自定义 lint  
- [x] App上线后 用户使用时卡顿 ，怎么查看是什么原因
- [x] 界面卡顿怎么排查和优化 ，何监测应用的 FPS
- [x] 网络优化  
- [x] 数据库优化
- [ ] view的优化，减少层级，异步加载，x2c框架引入，优缺点，textview的优化。
- [ ] 怎么优化xml inflate的时间，涉及IO与反射。了解 compose 吗？
- [ ] 讲下你做过的 首页优化
- [x] 如何提高线上代码质量 
- [x] 你们项目的稳定性如何？有做过什么稳定性优化的工作？
- [x] 你做过什么 性能优化 的工作
- [ ] 性能优化做过什么工作? 有用过什么工具？有没有精确测量的工具？
- [x] 如何进行单元测试，如何保证App稳定 
- [x] 代码质量如何控制
- [x] 技术选型是如何做 ，为什么这么考虑？从开发效率，产品性能，产品质量，产品体验等方面考虑
- [x] 你们网络框架用的什么？为什么？
- [ ] 如果 Android 和 iOS 调一个接口，一个通了一个没通 (或者响应速度一个块一个慢)，你会如何解决
- [x] 冷启动、热启动 优化方案
- [x] 有什么提高编译速度的方法



# 内存泄漏 、图片 、图片加载
- [ ] Drawable 与 View 有什么区别, Drawable 有哪些子类
- [x] 图片加载优化有什么经验吗
- [ ] 说一个你熟悉的图片加载库的图片加载流程
- [x] 图片内存的计算
- [x] 大图加载 原理和优化，长图加载
- [x] Bitmap高效加载 、Bitmap内存复用 inBitmap
- [x] inJustDecodeBounds 是什么？
- [x] Bitmap 内存 和 drawable 目录的关系
- [x] xhdpi 的图片放到 xxhdp 的手机上，内存会如何变化
- [ ] hdpi 和xxhdpi的手机，分别加载xhdpi下的图片，会缩放图片么？如果会缩放，是如何缩放的，像素点是如何补全或者减少的？图片在内存中的大小会如何变化？
- [x] Bitmap 不改变图片质量的情况下,如何减少内存
- [x] 图片显示不全、变形怎么处理
- [x] 一个 wrap_content 的 ImageView ，加载远程图片，传什么参数裁剪比较好?
- [x] Androidstudio drawable 下所以的格式都能转成 webp 么 ？哪些不能转 
- [x] 如何跨进程传递大图
- [ ] 图片的三级缓存中,图片加载到内存中,如果内存快爆了,会发生什么 ？怎么处理 
- [x] glide 默认 Bitmap 的 Config 配置是 ARGB_8888 么
- [ ] Glide 的缓存，有用过Glide的什么深入的API，自定义model是在Glide的什么阶段
- [ ] 你碰到过什么 内存泄漏 ，怎么处理
- [ ] 你认为为了避免内存泄漏，写代码时应该注意哪些事项
- [x] AsyncTask 内存泄露


# 打包发布
- [x] 项目搭建过程中有什么经验,有用到什么 gradle 脚本
- [x] Gradle 熟悉吗 ？ 常用哪些配置和命令
- [x] 如何通过 Gradle 配置多渠道包
- [x] 有没有编写过 Gradle 插件
- [x] 了解APK打包的过程吗 
- [x] Activity 会打包到包里面去吗
- [x] 分包有做什么操作


# 适配
- [x] 屏幕适配方案；头条适配方案核心原理。
- [x] 屏幕适配做过什么工作 
- [ ] Android11 有没有适配



# kotlin
- [ ] 有用 Anko DSL  写过布局吗 
- [ ] kotlin空安全的原理是什么？
- [ ] kotlinc与javac编译字节码有什么区别？
- [ ] kotlin lazy使用,lazy viewmodel
- [ ] kotlin 与Java互相调用有什么问题？
- [ ] kotlin ?的原理
- [x] 说说你对 协程 的理解， 可以完全取代 rxjava 吗 , 协程 可以在Java项目中使用吗 
- [x] 协程 怎么取消
- [ ] 讲一个 协程 的 scope 与 context，协程的+号 代表什么

# html5 
- [ ] .h5 与 native 交互做过什么工作
- [ ] 项目中的 Webview 与 native 通信
- [ ] h5 与 native 交互，webView.loadUrl 与 webView.evaluateUrl 区别
- [ ] 项目中对 WebView 的功能进行了怎样的增强
- [ ] native 如何对 h5 进行鉴权，让某些页面可以调，某些页面不能调
- [ ] @JavaScriptInterface为什么不通过多个方法来实现 
- [ ] 为什么不利用同步方法来做 jsBridge交互？ 同步可以做异步，异步不能做同步 
- [ ] jsBridge 实现方式
- [ ] CoordinatorLayout自定义behavior,可以拦截什么？
- [ ] 视频播放,一个player怎么实现预加载，避免loading
- [ ] webView加载本地图片，如何从安全方面考虑 
- [ ] webwiew了解？怎么实现和javascript的通信？相互双方的通信。@JavascriptInterface在？版本有bug，除了这个还有其他调用android方法的方案吗
- [ ] retrofit怎么做post请求


# 设计能力
- [ ] 如果让你来实现一个网络框架，你会考虑什么
- [ ] 你如何自己实现一个LRUCache？Android里面的LRUCache是如何实现的
- [ ] 设计一个上传日志的大小，涉及到，性能、文件大小、怎样压缩、时机、TopK 问题等
- [x] 如果产品要求你开发一个音频播放功能，你会怎么着手？预计会有什么坑？
- [x] 你觉得 XX 目前的APP有什么问题？
- [x] 如果叫你实现，你会怎样实现一个多主题的效果
- [x] 换皮肤、切换主题、暗黑模式
- [x] 如何封装一个字符串转数字的工具类

# 设计模式
- [x] 你在项目中有用到什么设计模式吗
- [x] 动态代理 的实现，动态代理有什么作用
- [x] 手写一个静态代理，一个动态代理
- [x] 代理模式 与装饰模式 的区别
- [x] 单例模式 有什么缺点
- [x] 单例的几种实现方式
- [x] DCL 双锁单例，为什么要价 volatile
- [x] 单例的 DCL 方式下，那个单例的私有变量要不要加volatile关键字，这个关键字有什么用
- [x] 手写 DCL 双检查单例模式，各个步骤有什么区别
- [x] Android中常用的设计模式，说三个比较高级的
- [x] android源码中有哪些设计模式
- [x] MVC 、MVP 、MVVM 理解
- [ ] 讲讲 MVC 、MVP ，presenter 内存泄漏的问题
- [ ] MVP 怎么处理内存泄漏
- [x] MVVM 双向数据绑定的原理是怎样的 
- [x] MVVM 怎么更新UI, databinding 用得多吗, databinding的原理
- [ ] ViewModel 是怎么实现双向数据绑定的 
- [ ] ViewModel 怎么实现自动处理生命周期 
- [ ] ViewModel 为什么在旋转屏幕后不会丢失状态
- [ ] ViewModel 的原理，为什么可以在Activity销毁后保存数据
- [ ] ViewModel 的使用中有什么坑
- [ ] 有没有看一下Google官方的 ViewModel demo
- [ ] ViewModel 在 Activity 初始化与在 Fragment中初始化，有什么区别 
- [x] 如果用了一些解耦的策略，怎么管理生命周期的


#  其他问题
- [x] 介绍一下你们项目的架构
- [x] 介绍一下你自已和项目 ，项目有什么难点  
- [x] 你们的项目中做过什么比较难的工作
- [x] 这些年有做一些什么比较难的工作
- [x] 说说为什么考虑离职 ,为什么考虑换工作 ，说说对你们原来公司的印象
- [x] 为什么考虑换一份工作 ，在你们公司这几年感觉怎么样
- [x] 你在团队中是怎样一个角色 ，有没有做什么推进项目的工作
- [x] 你们的产品为什么被砍掉，从哪方面考虑
- [x] 在这几年里，你有做过什么觉得最有价值的工作
- [x] 平常是怎么了解一些新知识与业界动态的，最近有什么印象深刻的文章
- [x] 你还有什么要问我的吗？目前有几个offer，倾向性是怎样的


# test for github