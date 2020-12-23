﻿本博客不保证完整性和正确性,请自行甄别。 左上角的 搜索功能 进行关键字搜索。 <br>
源码托管地址  https://gitee.com/hnyer/androidDev

#  博客分类
| 分类   |  
| ----- | --------- |  
| http://hnyer.gitee.io/androidDev |
| http://hnyer.gitee.io/nativeDev  //cpp jni |
| http://hnyer.gitee.io/webDev   //web 前端|
| http://hnyer.gitee.io/backDev   // 服务器搭建|
| http://hnyer.gitee.io/gameDev  |
| http://hnyer.gitee.io/pythondev  |
| http://hnyer.gitee.io/kotlindev  |
| http://hnyer.gitee.io/blockchainDev |
| http://hnyer.gitee.io/flutter-dev |
| https://blog.csdn.net/dengpanwen   //CSDN|
| https://hnyer.gitee.io   //红尘之声|


#  面试常见问题
- [x]  java 里带$的函数 是什么意思
- [x]  非对称加密 、对称加密
- [x]  硬件加速 是什么原理
- [x]  进程优先级
- [x]  Android APP的 入口函数 在哪里
- [x]  冷启动、热启动优化
- [x]  activity 启动模式
- [x]  Activity 生命周期有哪些，知道 onRestart 么； A启动B ，A的 onStop 一定会执行吗；按 Home 按键后的生命周期
- [x]  activity 启动另外一个activity的时候横屏切换再点击返回，过程中发生的生命周期
- [x]  Fragment#onHiddenChanged 是生命周期方法么？如何触发？
- [x]  Service的生命周期，两种启动方法，有什么区别
- [x]  有什么工具可以看到 Activity栈信息么？多个栈话，有方法分别得到各个栈的Activity列表么
- [x]  Android中常用的设计模式，说三个比较高级的
- [x]  MVVM 、MVP 
- [x]  如果用了一些解耦的策略，怎么管理生命周期的
- [x]  ANR 是怎么回事？怎么查？Service会引起 ANR 么 
- [x]  TCP和UDP区别，TCP为何是三次握手，为何是四次挥手
- [x]  为什么 Activity.finish() 之后 10s 才 onDestroy 
- [ ]  recycleview 原理
- [ ]  RecyclerView的特点和缓存
- [ ]  activity，Window，view的关系
- [ ]  activity有几个Window
- [ ]  savedInstanceState知道么，干什么用的，什么时候有值，什么时候为空，平时是怎么用的
- [ ]  SharedPrefrences的apply和commit有什么区别
- [ ]  ragment的replace和end？？的区别
- [ ]  ACTION_CANCEL什么时候触发，触摸button然后滑动到外部抬起会触发点击事件吗，在+ + 滑动回去抬起会么
- [ ]  怎么处理嵌套View的滑动冲突问题
- [ ]  多模块开发的时候不同的负责人可能会引入重复资源，相同的字符串，相同的icon等但是文件名并不一样，怎样去重？
- [ ]  canvas lock的缓冲区是怎么回事
- [ ]  有什么提高编译速度的方法
- [ ]  想改变listview的高度，怎么做
- [ ]  Synchronize关键字后面跟类或者对象有什么不同。
- [ ]  单例的DCL方式下，那个单例的私有变量要不要加volatile关键字，这个关键字有什么用
- [ ]  SparseArray和ArrayMap各自的数据结构，前者的查找是怎么实现的，与HashMap的区别
- [ ]  RecyclerView和ListView有什么区别？局部刷新？前者使用时多种type场景下怎么避免滑动卡顿。懒加载怎么实现，怎么优化滑动体验。
- [ ]  SQLite的数据库升级用过么
- [ ]  Scroller有什么方法，怎么使用的
- [ ]  webwiew了解？怎么实现和javascript的通信？相互双方的通信。@JavascriptInterface在？版本有bug，除了这个还有其他调用android方法的方案吗
- [ ]  Android 中Activity、Window、ViewRoot、DecorView之间的联系
- [ ]  View的分发机制，滑动冲突
- [ ]  多线程如何实现？有哪些方式 线程池的参数
- [ ]  你如何自己实现一个LRUCache？Android里面的LRUCache是如何实现的
- [ ]  synchronized和volatile的区别？为何不用volatile替代synchronized？类锁和对象锁互斥么
- [ ]  gcroot的类型
- [ ]  动态代理的实现。
- [ ]  MeasureSpec讲一下
- [ ]  Application中可以显示Dialog么？为什么？
- [ ]  泛型擦除，为何会有擦除？擦除的时机。通配符。
- [ ]  synchronized的同步原语
- [ ]  讲一下锁，synchronized和Lock。CAS原理
- [ ]  IdleHandler调用时机
- [ ]  glide默认Bitmap的Config配置是ARGB_8888么
- [ ]  Android11有没有适配
- [ ]  HashMap讲一下，数据结构、hash过程、扩容、加载因子为何是0.75等。
- [ ]  讲下Java的双亲委派
- [ ]  application中持有静态的用户信息，有何缺点？如何改进？
- [ ]  单例的几种实现方式：DCL、enum，静态内部类。还有饿汉式。懒汉式的使用场景：占用内存大、延迟初始化
- [ ]  hashmap。hash冲突时给链表插入数据，1.7头插法，1.8尾插法。
- [ ]  ArrayMap和SparseArray的区别，实现。
- [ ]  泛型：为何会有协变和逆变，PECS规则。
- [ ]  自定义View的几种方式。onMeasure、onLayout、onDraw方法都何时需要重写。
- [ ]  滑动冲突如何解决？有几种方式？具体从哪个事件开始拦截？在哪里拦截？比如双层ViewPager嵌套的滑动冲突如何解决。
- [ ]  Activity#setContentView中的xml文件是如何转化成View并显示到Activity中的。
- [ ]  PhoneWindow是在哪里初始化的
- [ ]  LayoutInflater是如何把xml布局文件转换成View对象的（反射）？View树如何生成的？怎么优化？
- [ ]  为什么会有R文件这个映射表？直接使用资源的路径不好么？
- [ ]  dex文件结构了解过么？为何会有65535的限制？mutildex技术了解么？这项技术的目的是什么？
- [ ]  Window和Activity的对应关系。除了Activity还有别的方式显示Window出来么？
- [ ]  requestLayout调用后，都会调用哪些方法？
- [ ]  绘制的数据是如何提交到远端的SurfaceFlinger
- [ ]  ContentProvider具体实现。
- [ ]  binderService方法中的回调具体运行在哪个线程？binder线程池最大线程数是多少？自定义的Callback远程调用，运行在哪个线程？为何不是主线程，如果运行在主线程会有哪些问题？
- [ ]  线程池如何配置，核心线程数你一般给多少
- [ ]  hdpi和xxhdpi的手机，分别加载xhdpi下的图片，会缩放图片么？如果会缩放，是如何缩放的，像素点是如何补全或者减少的？图片在内存中的大小会如何变化？
- [ ]  jetpack组件库使用过么？讲下具体组件
- [ ]  函数式编程如何理解？
- [ ]  t1、t2、t3三个线程，如何让三个线程按照顺序依次打印1-100。
- [ ]  悬浮窗如何实现
- [ ]  通知的类别
- [ ]  android aidl oneway用法_
- [ ]  ANR的log中关键字是什么
- [ ]  大图加载优化，原理。
- [ ]  Activity的onSaveInstance方法何时调用？它跟onPause、onStop的调用顺序如何？
- [ ]  讲下你做过的首页优化。
- [ ]  讲下leakCanary原理，为什么不用虚引用？引用队列里面存的是什么？
- [ ]  方法内部的匿名内部类，比如说给View设置的OnClickListener，它里面相关调用外部方法的形参，必须使用final修饰这个形参，为何？
- [ ]  Android里面进程间通信方式，ContentProvider可以用file实现么？
- [ ]  本地广播为何效率高？
- [ ]  讲下synchronized和volatile；读写锁和ReentrantLock，synchronized和读写锁的区别。
- [ ]  获取TextView的行数时，StaticLayout原理
- [ ]  MotionEvent#offsetLocation事件转发。
- [ ]  为什么会多次调用onMeasure和onLayout方法？
- [ ]  讲下onMeasure方法：
- [ ]  ①如何测量
- [ ]  ②测量模式
- [ ]  ③入参为什么是int类型？
- [ ]  讲下事件传递：
- [ ]  ①总体流程
- [ ]  ②DOWN事件拦截后，后续事件如何处理？
- [ ]  ③dispatchTouchEvent方法返回true后事件如何处理？
- [ ]  Lock的实现，以及与synchronized的区别
- [ ]  GCRoot，举例说明。比如说Activity和它的匿名内部类Handler，分析下引用链，对应的gcroot是哪个？
- [ ]  图片内存的计算。
- [ ]  ①在不影响图片质量的前提下，如何减少内存？
- [ ]  ②图片显示不全、变形怎么处理？
- [ ]  http和https：
- [ ]  ①它们的区别：https多了tls层。对称加密和非对称加密。
- [ ]  Application启动流程
- [ ]  ContentProvider启动流程
- [ ]  使用Application#onTrimMemory优化
- [ ]  使用ActivityLifecycleCallbacks做了哪些事情？
- [ ]  成员变量和局部变量的区别。为何成员变量需要jvm在对象初始话过程中赋默认值？
- [ ]  讲下equals和hashcode，他们为何必须一起重写？
- [ ]  Parcelable和Serializable本质区别，不要说用法，说原理。
- [ ]  Activity和Fragment的通信方式；系统为何会设计Fragment#setArgument方法。
- [ ]  View绘制流程；requestLayout和invalidate区别；invalidate每次都会触发onDraw么？View#onLayout每次会触发么？
- [ ]  屏幕适配方案；头条适配方案核心原理。
- [ ]  如何上传数据？请求头关键字段和请求体格式
- [ ]  MediaPlayer能同时播放多个音频么？如果需要播放多个提示音，如何实现？
- [ ]  drawable下所以的格式都能转成webp么？哪些不能转？
- [ ]  讲下arraylist、hashmap、linkedlist、linkedhashmap的实现。linkedhashmap为何会有这样的特性(lru)?它有个参数，表示命中率和使用次数。
- [ ]  lru是通过linkedhashmap实现的么？
- [ ]  线程的使用。讲下线程池的类型，线程池对象的参数，线程池最大线程数和核心线程数的关系，task的优先级如何实现？（优先级队列）
- [ ]  讲下事件分发。如果onInterceptTouchEvent返回true，但是onTouchEvent返回了false，是什么效果？如果还想让其他View接收事件，该怎么做？
- [ ]  大图加载；xhdpi的图片放到xxhdp的手机上，内存会如何变化；Bitmap内存复用
- [ ]  网络优化，数据库优化
- [ ]  ThreadLocal，LocalBroadcastReceiver实现
- [ ]  binder的mmap
- [ ]  Java内存模型
- [ ]  说一说Android中如何查看一个对象的回收情况 ？
- [ ]  Apk的大小如何压缩
- [ ]  如何通过Gradle配置多渠道包
- [ ]  Bitmap高效加载
- [ ]  内存泄漏
- [ ]  怎么获取view的宽高，如何确定值是准确的，说了view.post()补充 viewTreeObserverOnGlobalLayoutListener
- [ ]  ARouter路由原理
- [ ]  看视频的时候网络请求很慢怎么优化？
- [ ]  说说HTTP3.0有什么改进？
- [ ]  图片的三级缓存中,图片加载到内存中,如果内存快爆了,会发生什么？怎么处理？
- [ ]  描述一次跨进程通讯
- [ ]  首次 View 的绘制流程是在什么时候触发的？
- [ ]  ViewRootImpl 创建的时机？
- [ ]  ViewRootImpl 和 DecorView 的关系是什么？
- [ ]  DecorView 的布局是什么样的？
- [ ]  DecorView 的创建时机？
- [ ]  setContentView 的流程
- [ ]  LayoutInflate 的流程
- [ ]  Activity、PhoneWindow、DecorView、ViewRootImpl 的关系？
- [ ]  PhoneWindow 的创建时机？
- [ ]  如何触发重新绘制？
- [ ]  requestLayout 和 invalidate 的流程
- [ ]  requestLayout 和 invalidate 的区别

## 非技术问题
- [x]  代码质量如何控制
- [x]  技术选型是如何做
- [x]  如何提高线上代码质量 
- [x]  界面卡顿怎么排查和优化 ，何监测应用的 FPS
- [x]  App上线后 用户使用时卡顿 ，怎么查看是什么原因
- [ ]  如何进行单元测试，如何保证App稳定 