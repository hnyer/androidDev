# 代码混淆
[混淆规则 guardsquare](https://www.guardsquare.com/en/proguard)

##  androidstudio 混淆配置  
```text
// build.gradle 文件下 (这是系统默认生成的，可以修改)
1、导入 xxx\tools\proguard\proguard-android.txt 。 这里面是一些比较常规的不能被混淆的代码规则。
2、导入当前工程里面的 proguard-rules.pro 。这里是一些自定义的混淆规则。
proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'

//是否开启混淆 true or false
minifyEnabled true
```

##  不能混淆的内容
混淆了这些内容，会导致出错。
```text  
1、 自定义控件
2、 枚举
3、 第三方库中的类 （作者一般会标明）
4、 运用了反射的类
5、 使用了 Gson 之类的工具的实体类
6、 WebView 的 JS 的接口方法
7、 Parcelable 的子类和 Creator 静态成员变量不混淆
```

## 设置 不参与混淆
```text
# 保持该包下的类名 (子包下的会被混淆)
-keep class cn.wk.test.*
# 把本包和所含子包下的类名都保持
-keep class cn.wk.test.**
# 保持类名和里面的内容不被混淆
-keep class com.example.bean.** { *; }
```

## 自用的 aar 混淆
```text
# 代码混淆压缩比，在0~7之间，默认为5,一般不下需要修改
-optimizationpasses 5
# 混淆时不使用大小写混合，混淆后的类名为小写
-dontusemixedcaseclassnames
#  不混淆第三方引用的库  不忽略library里面非public修饰的类  ？？？
-dontskipnonpubliclibraryclasses
# 指定不忽略非public类里面的成员和方法
-dontskipnonpubliclibraryclassmembers
# 不做预检验， 去掉这一步可以加快混淆速度
-dontpreverify
# 把所有信息都输出，而不仅仅是输出出错信息
-verbose
#apk 包内所有 class 的内部结构 ??
-dump class_files.txt
# 列出了没有被混淆的类和成员
-printseeds seeds.txt
#列出从 apk 中删除的代码
-printusage unused.txt
#混淆前后的映射
-printmapping mapping.txt
# 混淆时所采用的算法 ，后面的参数是一个过滤器
# 这个过滤器是谷歌推荐的算法，一般不改变
-optimizations !code/simplification/artithmetic,!field/*,!class/merging/*
#保留Annotation不混淆
-keepattributes *Annotation*,InnerClasses
# 避免混淆泛型
-keepattributes Signature
# 保留异常
-keepattributes Exceptions
# 抛出异常时保留代码行号
-keepattributes SourceFile,LineNumberTable
#  不打印指定类的警告信息
-ignorewarnings

#==================================【项目配置】==================================
# 保留所有的本地native方法不被混淆
-keepclasseswithmembernames class * {
    native <methods>;
}

# 保留了继承自Activity、Application (四大组件等)这些类的子类
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class * extends android.view.View
-keep public class com.android.vending.licensing.ILicensingService
-keep public class * extends android.database.sqlite.SQLiteOpenHelper{*;}

# 如果有引用android-support-v4.jar包，可以添加下面这行
-keep public class com.null.test.ui.fragment.** {*;}
#如果引用了v4或者v7包
-dontwarn android.support.**
# 保留Activity中的方法参数是view的方法
-keepclassmembers class * extends android.app.Activity {
    public void * (android.view.View);
}

# 枚举类不能被混淆
-keepclassmembers enum * {
    public static **[] values();
    public static ** valueOf(java.lang.String);
}

#   不混淆反射
-keepattributes EnclosingMethod

# 保留自定义控件(继承自View)不能被混淆
-keep public class * extends android.view.View {
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
    public void set*(***);
    *** get* ();
}
# 保留Parcelable序列化的类不能被混淆
-keep class * implements android.os.Parcelable{
    public static final android.os.Parcelable$Creator *;
}
# 保留Serializable 序列化的类不被混淆
-keepclassmembers class * implements java.io.Serializable {
   static final long serialVersionUID;
   private static final java.io.ObjectStreamField[] serialPersistentFields;
   !static !transient <fields>;
   private void writeObject(java.io.ObjectOutputStream);
   private void readObject(java.io.ObjectInputStream);
   java.lang.Object writeReplace();
   java.lang.Object readResolve();
}
# 对R文件下的所有类及其方法，都不能被混淆
-keepclassmembers class **.R$* {
    *;
}
# 对于带有回调函数onXXEvent的，不能混淆
-keepclassmembers class * {
    void *(**On*Event);
}

#==================================【根据自己的项目（自己写的代码），配置需要 忽略混淆的类==================================
-keep class org.litepal.** { *; }
-keep class com.walkera.wktools.** { *; }
#==================================【根据自己的项目（自己引用的第三方jar），配置需要 忽略混淆的类==================================
# ant.jar
#-libraryjars libs/ant.jar
-keep class  org.apache.** { *; }
# ftp4j.jar
#-libraryjars ./libs/ftp4j-1.7.2.jar
-keep class  it.sauronsoftware.** { *; }
# guava.jar
#-libraryjars ./libs/guava-17.0.jar
-keep class  com.google.** { *; }
# netty.jar
#-libraryjars ./libs/netty-all-4.0.25.Final.jar
-keep class  io.netty.** { *; }
# nineoldandroids.jar
#-libraryjars ./libs/nineoldandroids-2.4.0.jar
-keep class  com.nineoldandroids.** { *; }
```

# Gradle 配置
## Gradle 是什么
```text
Gradle 是一款强大的 构建工具 ，而不是语⾔。
它使用了 Groovy 这个语言，创造了一种 DSL ，但它本身不是语⾔。
```

## Gradle 打包提速方案
### Gradle 升级到 最新的版本
```text
将 Gradle 和 Android Gradle Plugin 的版本升至最新，
所带来的的构建速度的提升效果是显而易见的，
特别是当之前你所使用的版本很低的时候。
```


### Gradle 开启离线模式
```text
打开 Android Studio 的离线模式后，
所有的编译操作都会走本地缓存 ,
这将会极大地缩短编译时间。
尤其是中国大陆的网络访问外网龟速的情况下。
```


###  增加 Androidstudio 的内存空间 、并行的处理器核心数 
```text
在性能好的电脑上给 AS 一个大的内存，会让 as 有更好的表现。
maxProcessCount // cpu核心数
```


### 删除不必要的 Moudle 、合并部分 Module
```text
过多的 Moudle 会使项目中 Module 的依赖关系变得复杂，
Gradle 在编译构建的时候会去检测各个 Module 之间的依赖关系，
它会去梳理这些 Module 之间的依赖关系，
以避免 Module 之间相互引用而带来的各种问题。
除了删除不必要的 Moudle 或合并部分 Module 的方式外，
我们也可以将稳定的底层 Module 打包成 aar，
上传到公司的本地 Maven 仓库，通过远程方式依赖。
```


### 删除 项目中的无用资源
```text
1、如果我们不需要写单元测试代码，可以直接删除 test 目录。
2、如果我们不需要写 UI 测试代码，也可以直接删除 androidTest 目录。
3、此外，如果 Moudle 中只有纯代码，可以直接删除 res 目录。

在 Android Studio 中提供 Remove Unused Resource 功能，
将一些无用的图片 等资源删除，
减少 gradle 分析时间。
```


### 优化第三方库的引用
```text
1、使用更小的库去替换现有的同类型的三方库。
2、使用 exclude 来排除三方库中某些不需要或者是重复的依赖。
3、使用 debugImplementation 来依赖仅在 debug 期间才会使用的库，
如一些线下的性能检测工具。 
```


### resConfigs 去除多余的语言资源 
```text
如果你使用的库包含语言资源(例如使用的是AppCompat或 Google Play服务)， 
则APK将包括这些库中消息的所有已翻译语言字符串，
无论应用的其余部分是否翻译为同一语言。

如果你想只保留应用正式支持的语言，则可以利用 resConfigs 属性指定这些语言。
系统会移除未指定语言的所有资源。
```



### 使用增量编译 (新版gradle 已经开启 ，属性已经废弃)
```text
在 Gradle 4.10 版本之后便默认使用了增量编译 。
```


## Gradle 常用命令
```text
// gradlew -v   
查看 gradle 版本

// gradlew clean
删除 build 文件夹 

// gradlew clean build --refresh-dependencies
强制更新最新依赖，清除构建后再构建

// gradlew build
编译 打包 (debug 和 release) 

// gradlew build --info
编译打包并打印日志

// gradlew build --profile
编译并输出性能报告，一般在构建工程根目录 build/reports/profile

//  gradlew build --info --debug --stacktrace
调试模式构建并打印堆栈日志

// gradlew assembleRelease
// gradlew aR  (简化版命令 )
编译并打 Release 的包    
     
// gradlew assembleDebug
// gradlew aD (简化版命令 )
编译并打 Debug 包    

// gradlew installDebug
debug 模式打包并安装

// gradlew installRelease
Release 模式打包并安装

// gradlew uninstallRelease
卸载 Release 模式包

// gradlew assemble
debug 、release 模式全部渠道打包
 
//  gradlew app:dependencies  > xxx.txt
查找 app模块的依赖关系，并输出到 xxx.txt文件中。
```


## gradle 诊断报告工具
### Profile report
```text
我们一般会使用如下命令来生成一份本地的构建分析报告：
gradlew assembleDebug --profile
xxx/build/reports/profile/profile-xxx.html
可以查看各个模块、依赖、任务的执行时间。 
```


### Build Scan
```text
一个更细致的构建汇报工具，
gradlew build --scan
命令执行完成后，需要将一些信息上传到 https://scans.gradle.com 上，
然后填写一个 email 地址接收最后的 scan 报告。
```

![](../pics/buildscan.png)


# 渠道包
## 为什么需要打渠道包
```text
1、为了区分用户从哪个 渠道下载的，比如官网、应用宝、华为、小米等商店，
因为一般是分渠道进行推广的，这样就能统计出每个渠道的推广效果进而进行调整。

2、可以针对每个不同渠道的 APP配置不同的 APP名字 和APP logo、包名。
例如 搜狗输入法小米版、搜狗输入法华为版。
```
##  多渠道 打包 方案
目前公司APP集中于官网发布，暂时用不上。需要的时候再来更新具体使用方法。
```text
1、gradle 官方方案。
2、其他一些第三方的插件，例如美团就有一个 
Walle（瓦力）https://github.com/Meituan-Dianping/walle
```

# Sqlite 数据库升级
```text
public class XXXHelper extends SQLiteOpenHelper {
    /**
     * 1、在第一次打开数据库的时候才会走
     * 2、在清除数据之后再次运行-->打开数据库，这个方法会走
     * 3、没有清除数据，不会走这个方法
     * 4、数据库升级的时候这个方法不会走
     */
    @Override
    public void onCreate(SQLiteDatabase db) {   }
    /**
     * 1、这个方法只有当数据库已经存在，而且版本升高的时候，才会调用
     * 2、第一次创建数据库的时候，这个方法不会走
     * 3、清除数据后再次运行(相当于第一次创建)这个方法也不会走
     */
    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {   }
}

onCreate() onUpgrade() 是数据库创建和升级的重要函数，在里面进行 sql 语句操作即可。
只是 在升级的逻辑会比较麻烦和繁琐， 因为升级情况会比较多，
比如用户可能是从 版本1 、2、3、4... 升级到版本10的，要针对这几种情况都要做相应的逻辑。

如果不想写sql 语句，可以使用 郭霖 开源的 LitePal Android 数据库。
封装的比较好 ，增删改查、数据库升级 事务操作都有。
```