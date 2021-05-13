# App组件化开发
## 组件化开发解决的痛点
```text
1、项目模块多且复杂，编译一次要5分钟甚至10分钟？太慢不能忍？
2、改了一行代码 或只调了一点UI，就要run整个项目，再忍受一次10分钟？
3、合代码经常发生冲突？很烦？
4、被人偷偷改了自己模块的代码？很不爽？
5、做一个需求，发现还要去改动很多别人模块的代码？
6、别的模块已实现的类似功能，自己要用只能去复制一份代码再改改？
7、“这个不是我负责的，我不管”，代码责任范围不明确？
8、只做了一个模块的功能，但改动点很多，所以要完整回归测试？
9、做了个需求，但不知不觉导致其他模块出现bug？
```

## 组件化开发的关键特征
```text
组件化的核心就是解耦，
每个业务组件是互相独立的，不存在直接的依赖关系。
比如登录组件、支付组件是互不影响的。
```


## 组件独立调试 (单工程模式 - 单人开发，推荐)
```text
// 输出 apk (调试阶段配置)
com.android.application
// 输出 aar (集成阶段配置)
com.android.library

// 批量自动切换
1、在根  build.gradle 中做一个全局变量
ext {
    isLibrary  = true
}

2、在组件 build.gradle 中配置
def isLibrary =  rootProject.isLibrary
if (isLibrary.toBoolean()){
    apply plugin: 'com.android.library'
}else {
    apply plugin: 'com.android.application'
}

defaultConfig {
    if(!isLibrary.toBoolean()){
        applicationId "com.wkreader.base"
    }
}
sourceSets {
    main {
        if (isLibrary.toBoolean()) {
            manifest.srcFile 'src/main/AndroidManifestlib.xml'
        } else {
            manifest.srcFile 'src/main/AndroidManifest.xml'
        }
    }
}
```

## 组件独立调试 (多工程模式 - 多人开发，推荐)
```text
相比较单工程，多工程模式非常简单，不需要上述配置，不讲解。
```


## 组件页面跳转 、通信 框架
```text
组件之间没有依赖，不能使用显示启动来打开对方的Activity，
隐式启动是可以实现跳转，但是隐式 Intent 需要通过 AndroidManifest 配置和管理，协作开发显得比较麻烦。
这里一般采用业界通用的方式—路由。

业界采用的路由框架有阿里的 ARouter 、美团的 WMRouter 。
```

## ARouter 基本使用
https://github.com/alibaba/ARouter/blob/master/README_CN.md
```text
1、路由跳转是子模块都需要用到的，所以我们在 最基础模块 lib_base 中引入，
各个模块会引用这个lib_base。
compile "com.alibaba:arouter-api:$rootProject.arouterVersion" 

2、然后在各子模块的build文件中引入：
annotationProcessor "com.alibaba:arouter-compiler:$rootProject.arouterProcessorVersion"
defaultConfig {
javaCompileOptions {
    annotationProcessorOptions {
        arguments = [AROUTER_MODULE_NAME: project.getName()]
    }
 }
}

3、如果工程启用了混淆机制，需要keep相关部分，否则会找不到路径
-keep public class com.alibaba.android.arouter.routes.**{*;}
-keep public class com.alibaba.android.arouter.facade.**{*;}
-keep class * implements com.alibaba.android.arouter.facade.template.ISyringe{*;}
-keep interface * implements com.alibaba.android.arouter.facade.template.IProvider
 -keep class * implements com.alibaba.android.arouter.facade.template.IProvider

4、注意配置路径的时候，如果是在不同的模块里，第一级目录不能相同
public static final  String BookshelfActivity="/lib_reader/BookshelfActivity" ;
public static final  String EnglishListActivity="/lib_english/EnglishListActivity" ;

5、Activity 跳转
// 应用内简单的跳转 
ARouter.getInstance().build("/test/activity").navigation();

// 携带参数跳转
ARouter.getInstance().build("/test/1")
            .withLong("key1", 666L)
            .withString("key3", "888")
            .withObject("key4", new Test("Jack", "Rose"))
            .navigation();
```


## ARouter 路由原理
```text

# 跳转原理
ARouter 为了解耦, 在组件之间没有依赖时也可以彼此跳转。

ARouter 中使用了 javapoet 注解处理器，生成额外的Java文件 ，
 将注解的key与类的路径通过一个Map关联起来了，
要我们拿到这个Map,即可在运行时通过注解的key拿到类的路径 ，
ARouter所做的即是在初始化时利用反射扫描指定包名下面的所有className，然后再添加map中 。

ARouter的缺陷在于首次初始化时会通过反射扫描dex，同时将结果存储在SP中，会拖慢首次启动速度
4.ARouter提供了插件实现在编译期实现路由表的自动加载，从而避免启动耗时，其原理是 ASM 字节码插桩

# 路由表加载原理
```

## ARouter 组件之间通信、组件和APP主模块之间通信 ( 通过暴露服务 IProvider )
```text
// 定义服务
public interface ILoginProvider extends IProvider {
    boolean isLogined() ;
}

@Route(path = ProviderConfig.LoginInfoProvider, name = "登录数据服务")
public class LoginInfoProvider implements ILoginProvider {
    public void init(Context context) {   }
    public boolean isLogined(){
        return true ;
    }
}

// 发现和使用服务
ILoginProvider service =  (ILoginProvider) ARouter.getInstance().build(ProviderConfig.LoginInfoProvider).navigation();
boolean isLogined = service.isLogined( ) ;

// 使用步骤
1、在公共的基础lib中定义服务接口，
2、在各自的lib中继承这个接口并实现具体功能
3、在其他模块中调用接口
```

## ARouter 预处理服务 
```text
跟页面拦截差不多，就是在调用服务之前预先处理一下。
比如 想调用支付模块的服务，就可以先判断你是否已经登录，不登录就禁止调用
 @Route(path = RouteUtils.MyPretreatmentService)
 public class MyPretreatmentService implements PretreatmentService {
     @Override
     public boolean onPretreatment(Context context, Postcard postcard) {
         String path = postcard.getPath() ;
         if(path.equals(RouteUtils.EnglishListActivity)){
             boolean isLogined = xx ;
             if (!isLogined){
                 Toast.makeText(mcontext   , "请先登录", Toast.LENGTH_SHORT).show() ;
                 return  false ;
             }
         }
         return true;
     }
     private Context mcontext ;
     @Override
     public void init(Context context) {
         this.mcontext = context ;
     }
 }
```


## ARouter 页面拦截 
```text
比较经典的应用就是在跳转过程中处理登陆事件，这样就不需要在目标页重复做登陆检查 。
拦截器会在跳转之间执行，多个拦截器会按优先级顺序依次执行。
@Interceptor( priority = 8 , name = "登录检验拦截器")
public class LoginInterceptor implements IInterceptor {
    @Override
    public void process(Postcard postcard, InterceptorCallback callback) {
        boolean isLogined =  false;
        callback.onContinue(postcard);
        if (isLogined) {
            callback.onContinue(postcard); //不拦截
        } else {
            String path = postcard.getPath();
            switch (path) {
                case  RouteUtils.EnglishListActivity:
                    callback.onInterrupt( new RuntimeException("还未登录")); //需要登陆拦截
                    break;
                default:
                    callback.onContinue(postcard); //不拦截
                    break;
            }
        }
    }

    @Override
    public void init(Context context) {
        // 会在sdk初始化的时候调用该方法
    }
}

ARouter.getInstance().build(RouteUtils.EnglishListActivity).navigation( context , new NavigationCallback....
// 如果被拦截器拦截了，NavigationCallback 的函数 onInterrupt() 会被调用...
```


 



## ARouter 降级策略 
一个奇怪的术语。感觉术语名字和功能不是很匹配。
```text
因为 arouter 框架activity 跳转是需要配置路由表的，
所以可能因为粗心导致路由表配置错误的现象，
为了解决这个问题，所以搞了一个 所谓降级策略来补救，
给用户一个好一点的体验，而不至于点击后没有任何反应。
// 全局监测
@Route(path = RouteUtils.MyDegradeService)
public class MyDegradeService implements DegradeService {
    @Override
    public void onLost(Context context, Postcard postcard) {
        WkLog.showLog("检测到无效跳转: " + postcard.getPath());
    }

    @Override
    public void init(Context context) {

    }
}

// 2、独立跳转
ARouter.getInstance().build(RouteUtils.EnglishListActivity).navigation( context , new NavigationCallback....
如果路由路径错误，NavigationCallback 的 onLost() 会被回调
```

 
 
 ##  ARouter 重写跳转URL 、重定向
 ```text
就是将传经来的 路由地址 按照你的规则修改一下，再去执行，
目前我不知道有哪些应用场景，先不用。
PathReplaceService 
```

 
 