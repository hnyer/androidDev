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