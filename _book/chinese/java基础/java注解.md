# java 注解
```text
注解是在 Java 1.5 中开始引入的概念。

注解配合反射可以在 运行时 处理注解、
配合 apt tool可以在编译期处理注解。
在JDK1.6的时候，apt (Annotation Processing Tool 注解处理工具) 被整合到了javac 中。

注解本身只起到一个标记和传值的作用，
有没有注解都不影响程序的运行，
注解的作用取决于对于注解元素的处理。

得当的使用注解，可以极大的提高开发效率，避免编写重复、易错的代码。
```

## 元注解
```text
元注解是一种基本注解，它能够应用到其它的注解上面。
```

### @Retention
```text
说明了这个注解的的存活时
@Retention(RetentionPolicy.RUNTIME) 
@Retention(RetentionPolicy.SOURCE ) 
@Retention(RetentionPolicy.CLASS )
```


### @Documented
```text
能将注解中的元素包含到Javadoc中去
```

### @Target
```text
指定了注解运用的地方
@Target(ElementType.ANNOTATION_TYPE ) 
//可以给一个注解进行注解 
@Target(ElementType.CONSTRUCTOR ) 
//可以给构造方法进行注解 
@Target(ElementType.FIELD ) 
// 可以给属性进行注解 
@Target(ElementType. LOCAL_VARIABLE) 
//可以给局部变量进行注解 
@Target(ElementType.METHOD ) 
// 可以给方法进行注解 
@Target(ElementType. PACKAGE) 
//可以给一个包进行注解 
@Target(ElementType.PARAMETER ) 
//可以给一个方法内的参数进行注解 
@Target(ElementType.TYPE ) 
//可以给一个类型进行注解，比如类、接口、枚举
```

### @Inherited
```text
用于放在注解上 。
当 @Inherited 注解加在某个类A上时，假如类B继承了A，则B也会带上该注解。
```

### @Repeatable 
```text
Java 1.8 才有
重复注解， 注解的值可以同时取多个

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.CLASS)
public @interface Role {
    String role() default "大佬";
}

//如果 Role 注解没有被 Repeatable 修饰的话，以下会报错的
@Role(role = "小兵")
@Role(role = "大佬")
public Person person ;
```

 
## java 部分内置注解
### @SuppressWarnings  
```text
阻止编译器报警告提示
@SuppressWarnings("all") 
// 抑制所有警告 
@SuppressWarnings("boxing") 
// 抑制装箱、拆箱操作时候的警告 
@SuppressWarnings("cast") 
// 抑制映射相关的警告 
@SuppressWarnings("dep-ann") 
// 抑制启用注释的警告 
@SuppressWarnings("deprecation")
// 抑制过期方法警告 
@SuppressWarnings("fallthrough") 
// 抑制确在switch中缺失breaks的警告 
@SuppressWarnings("finally") 
// 抑制finally模块没有返回的警告 
@SuppressWarnings("hiding") 
// 忽略 关于隐藏的本地变量的警告 
@SuppressWarnings("incomplete-switch") 
// 忽略没有完整的switch语句 
@SuppressWarnings("nls") 
// 忽略非nls格式的字符 
@SuppressWarnings("null") 
// 忽略对null的操作 
@SuppressWarnings("rawtypes") 
// 使用generics时忽略没有指定相应的类型 
@SuppressWarnings("restriction") 
// 忽略 使用不建议或者禁止的引用的警告 
@SuppressWarnings("serial") 
// 忽略在serializable类中没有声明serialVersionUID变量 
@SuppressWarnings("static-access") 
// 抑制不正确的静态访问方式警告 
@SuppressWarnings("synthetic-access") 
// 抑制子类没有按最优方法访问内部类的警告 
@SuppressWarnings("unchecked") 
// 抑制没有进行类型检查操作的警告 
@SuppressWarnings("unqualified-field-access") 
// 抑制没有权限访问的域的警告 
@SuppressWarnings("unused") 
// 抑制没被使用过的代码的警告
```

### @SafeVarargs 
```text
JDK 7 才有。
阻止编译器产生 unchecked 这样的警告，效果跟 @SuppressWarnings("unchecked")相同
使用了这种注解后，开发者要确保自己的类型转换是没有问题的，否则运行时可能会报错。
```

### @FunctionalInterface  
```text
Java 1.8 才有 。
当你写的接口不符合函数式接口定义的时候，编译器会报错。
```
 

## 注解的属性 (成员变量)
```text
注解本身只起到标记的作用，
如果想要给标记的对象传递数据，还需要给注解定义一些属性 。
注意 注解只有属性，没有方法。

注解的属性在注解的定义中以“无形参的方法”形式来声明，
其方法名定义了该 属性 的名字，其返回值定义了该 属性 的类型。

@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    // 给 TestAnnotation 注解定义了2个属性 age 、name
    public int age() default -1; // 无参方法 age()
    public String name() default "aivin";
}
// 使用注解的属性
@TestAnnotation( name = "tom",age = 22)
public Person person;
```

# 运行时注解
```text
运行时注解就是在程序运行的时候通过反射获取到注解然后做处理。

在Java语言中， 
所有实现了 java.lang.reflect.AnnotatedElement 接口 的元素，都是可以被注解的元素 
例如 Class，Method，Field，Constructor，Package 等，
都可以利用 反射机制获得它的注解 。
``` 

##自定义 运行时注解
```text
一般在制作自己的框架或者某种小工具时，我们可以使用自定义注解。
注意, 如果仅仅自定义了注解，并使用在代码中。这些注解并不会起作用。
如果想要真正使用上它们，你还得利用反射等原理写一个工具类来解析它。
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    public int age() default -1;
    public String name() default "aivin";
}

@TestAnnotation (name = "类设置的名字")
public class Person {
    @TestAnnotation( name = "属性设置的名字",age = 22)
    public Student mystudent;
    @TestAnnotation( name = "方法设置的名字",age = 23)
    public void setInfo(int age ,String name){
    }
}

public static void getFruitInfo( Class<?> clz ){
    boolean isAnnota = clz.isAnnotationPresent(TestAnnotation.class);
    if(isAnnota){
        TestAnnotation testAnnotation = clz.getAnnotation(TestAnnotation.class);
        System.out.println(clz.getSimpleName()+"--->"+testAnnotation.name());
        System.out.println(clz.getSimpleName()+"--->"+testAnnotation.age());
    }else{
        System.out.println("类没有使用注解" );
    }

    Field[] fields = clz.getDeclaredFields();
    for(Field field :fields){
        field.setAccessible(true);
        // Field field =clz.getDeclaredField("mystudent"); // 获取指定变量的属性
        TestAnnotation testAnnotation1 = field.getAnnotation(TestAnnotation.class);
        if ( testAnnotation1 != null ) {
            System.out.println(clz.getSimpleName()+" ->"+ field.getName()+ "--->"+testAnnotation1.name());
            System.out.println(clz.getSimpleName()+" ->"+ field.getName()+"--->"+testAnnotation1.age());
        }
    }

    Method[] methods = clz.getDeclaredMethods();
    for(Method method : methods){
        method.setAccessible(true);
        //clz.getDeclaredMethod("setInfo" ,int.class ,String.class); // 获取指定方法
        TestAnnotation testAnnotation2 = method.getAnnotation(TestAnnotation.class);
        if ( testAnnotation2 != null ) {
            System.out.println(clz.getSimpleName()+" ->"+ method.getName()+ "--->"+testAnnotation2.name());
            System.out.println(clz.getSimpleName()+" ->"+ method.getName()+"--->"+testAnnotation2.age());
        }
    }
}
 
// 测试 
@Test
public void myTest() {
    MyTool.getFruitInfo( Person.class);
}
```
## 运行时注解 的不足
```text
运行时注解 可能会因为java反射而引起较为严重的性能问题 。
当然因为不用生成额外的代理文件，与编译时注解相比，
可以减少apk的大小。
```

 
# 编译时注解
```text
apt 在编译时获取注解，以我们的源码作为输入，获取注解中的信息，重新输出一份java代码。
因为在是在编译器就将最终的代码生成了，所以并不会像反射那样 影响性能。

android-apt 作者在官网发表声明证实了后续将不会继续维护 android-apt，
并推荐大家使用 Android 官方插件提供的相同能力。
Android Gradle 插件提供了名为 annotationProcessor 的功能来完全代替 android-apt。
``` 

## 自定义 编译时注解 ( 基于Androidstudio)
```text
 自定义编译时注解流程与原理
 1、自定义注解
 2、自定义 处理器 并注册 ( extends AbstractProcessor )
 3、编译生成代理文件，并打包到apk(插件会自动打包到apk)
 4、代码中直接调用代理类。
 
注解处理器是一个在javac编译期处理注解的工具，
在编译期你创建的处理器以Java代码作为输入，生成文件.java文件作为输出。 
新的Java文件 本质上就是一个代理类，需要开发在开发中手动调用代理类。
注解处理器不能修改已经存在的Java类 (不能向已有的类中添加方法),只能生成新的Java类。
 
在编译期扫描 .java 文件的注解，并传递到注解处理器，
注解处理器可根据注解生成新的.java文件，
这些新的.java问和原来的.java一起被 javac编译。 

在 Androidstudio 中 使用 编译时注解，
我们需要 依赖  com.google.auto.service:auto-service 
和 rt.jar 的 AbstractProcessor 来实现。
```

## 自定义 编译时注解 demo
```text
解析 注解并生成 新的Java文件，有两种方式
1、手动拼接 (能更深刻地了解流程)
2、使用 javapoet 插件 生成 (能简化代码)

// 注解模块依赖 
// 因为一些类 Android系统中已经删除，所以只能是 java 类型的lib
implementation 'com.google.auto.service:auto-service:1.0-rc3' 
annotationProcessor 'com.google.auto.service:auto-service:1.0-rc6' 
implementation 'com.squareup:javapoet:1.8.0' 
// app 模块的依赖
annotationProcessor project(':butterknife_compiler') 
implementation project(':butterknife_compiler') 

demo地址 https://gitee.com/hnyer/annotation-test
```
 
# Java反射
```text
JAVA反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；
对于任意一个对象，都能够调用它的任意方法和属性；
这种动态获取信息以及动态调用对象方法的功能称为java语言的反射机制。
```

## 反射可以 修改 final 修饰的字段吗
```text
对静态的final修饰的字段(赋值或者不赋值)进行修改，
都会报错：java.lang.IllegalAccessException错误。

对于非静态的final成员变量，在没有赋值的情况下是可以使用反射对其进行赋值的；
对于已经初始化赋值的变量，反射不能真正该变变量的值，
虽然用反射 fieldXX.get(bean)方法可以获得新值，但是用对象实例访问得到的是旧值。

private final String v1 = null;
private final String v2 = "v2";
FinalDemo finalDemo = new FinalDemo();
Field f1 = finalDemo.getClass().getDeclaredField("v1");
Field f2 = finalDemo.getClass().getDeclaredField("v2");
f1.setAccessible(true);
f2.setAccessible(true);
f1.set(finalDemo, "new_v1");
f2.set(finalDemo, "new_v2");
finalDemo.getV1()); // new_v1
f1.get(finalDemo)); // new_v1
finalDemo.getV2()); // V2
f2.get(finalDemo)); // new_v2
```

###  android 反射库
```text
Android P (android 9)引入了针对非 SDK 接口（俗称为隐藏API）的使用限制。
这是继 Android N (Android 7)上针对 NDK 中私有库的链接限制之后的又一次重大调整。
从今以后，不论是native层的NDK还是 Java层的SDK，我们只能使用Google提供的、公开的标准接口。

FreeReflection 是一个破解反射限制的库
FreeReflection is a library that lets you use reflection without any restriction above Android P (includes Q and R).
https://github.com/tiann/FreeReflection
```


# Android 注解库
如果代码运行时不符合注解指定的条件就会报错，
比如标记了是 @MainThread  ，但是在子线程中运行，就会报错
```text
implementation 'com.android.support:support-annotations:28.0.0'
如果有添加 androidx 、material 等依赖，无需再添加 annotations 依赖
implementation 'androidx.appcompat:appcompat:1.0.2'
implementation 'com.google.android.material:material:1.2.0'
```
 

| 注解名                   | 释义                                                                                                                       | 修饰类型                                           |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------|
| @AnimatorRes              | 用于描述一个整型的参数或字段，或者是一个期望返回 animator 资源引用的方法（例如：android.R.animator.fade_in）               | 参数、字段、方法                                   |
| @AnimRes                  | 用于描述一个整型的参数或字段，或者是一个期望返回 anim 资源引用的方法（例如：android.R.anim.fade_in）                       | 参数、字段、方法                                   |
| @AnyRes                   | 用于描述一个整型的参数或字段，或者是一个期望返回任意资源引用的方法                                                         | 参数、字段、方法                                   |
| @AnyThread                | 被注解的元素可以在任意线程被调用                                                                                           | 方法、构造器、类、接口、枚举                       |
| @ArrayRes                 | 用于描述一个整型的参数或字段，或者是一个期望返回 array 资源引用的方法（例如：android.R.array.phoneTypes）                  | 参数、字段、方法                                   |
| @AttrRes                  | 用于描述一个整型的参数或字段，或者是一个期望返回 attr 资源引用的方法（例如：android.R.attr.action）                        | 参数、字段、方法                                   |
| @BinderThread             | 用于描述一个方法、构造器、类、接口、枚举应该运行在 binder 线程                                                             | 方法、构造器、类、接口、枚举                       |
| @BoolRes                  | 用于描述一个整型的参数或字段，或者是一个期望返回 boolean 资源引用的方法                                                    | 参数、字段、方法                                   |
| @CallSuper                | 用于描述子类方法，希望它们重写父类方法时也要通过 super.() 调用父类方法                                                     | 方法                                               |
| @CheckResult              | 用于描述一个方法，如果它的返回值被忽略了，那么就会报错                                                                     | 方法                                               |
| @ColorInt                 | 用于描述一个整型的参数或字段，或者是一个期望返回 int 类型颜色值的方法                                                      | 参数、字段、方法                                   |
| @ColorLong                | 用于描述一个整型的参数或字段，或者是一个期望返回 long 类型颜色值的方法                                                     | 参数、字段、方法                                   |
| @ColorRes                 | 用于描述一个整型的参数或字段，或者是一个期望返回 color 资源引用的方法（例如：android.R.color.black）                       | 参数、字段、方法                                   |
| @DimenRes                 | 用于描述一个整型的参数或字段，或者是一个期望返回 dimension 资源引用的方法（例如：android.R.dimen.app_icon_size）           | 参数、字段、方法                                   |
| @Dimension                | 用于描述一个整型的参数或字段，或者是一个期望返回 dimension 的方法                                                          | 参数、字段、方法、注解                             |
| @DrawableRes              | 用于描述一个整型的参数或字段，或者是一个期望返回 drawable 资源引用的方法（例如：android.R.attr.alertDialogIcon）           | 参数、字段、方法                                   |
| @FloatRange               | 描述一个参数、字段或方法的返回值是一个指定范围内的 float 或 double 类型的值                                                | 参数、字段、方法                                   |
| @FractionRes              | 用于描述一个分数类型的参数或字段，或者是一个期望返回 fraction 资源引用的方法                                               | 参数、字段、方法                                   |
| @HalfFloat                | 用于描述一个半精度类型的参数或字段，或者是一个期望返回半精度值的方法                                                       | 参数、字段、方法                                   |
| @IdRes                    | 用于描述一个整型的参数或字段，或者是一个期望返回 id 资源引用的方法（例如：android.R.id.copy）                              | 参数、字段、方法                                   |
| @IntDef                   | 用于描述一个注解，然后再用这个被描述的注解去描述一个整形的参数或字段，或者是一个期望返回值是显示声明常量之一的方法         | 注解                                               |
| @IntegerRes               | 用于描述一个整型的参数或字段，或者是一个期望返回 integer 资源引用的方法（例如：android.R.integer.config_shortAnimTime）    | 参数、字段、方法                                   |
| @InterpolatorRes          | 用于描述一个整型的参数或字段，或者是一个期望返回 integer 资源引用的方法（例如：android.R.interpolator.cycle）              | 参数、字段、方法                                   |
| @IntRange                 | 描述一个参数、字段或方法的返回值是一个指定范围内的 int 类型的值                                                            | 参数、字段、方法                                   |
| @Keep                     | 被注解的元素不会被混淆                                                                                                     | 包、参数、字段、方法、类、接口、枚举、注解、构造器 |
| @LayoutRes                | 用于描述一个整型的参数或字段，或者是一个期望返回 layout 资源引用的方法（例如：android.R.layout.list_content）              | 参数、字段、方法                                   |
| @MainThread               | 被注解的元素只能在主线程被调用                                                                                             | 方法、构造器、类、接口、枚举                       |
| @MenuRes                  | 用于描述一个整型的参数或字段，或者是一个期望返回 menu 资源引用的方法（例如：android.R.menu.content）                       | 参数、字段、方法                                   |
| @NonNull                  | 用于描述一个参数或字段、或者一个方法的返回值不为空                                                                         | 参数、字段、方法                                   |
| @Nullable                 | 用于描述一个参数或字段、或者一个方法的返回值可为空                                                                         | 参数、字段、方法                                   |
| @PluralsRes               | 用于描述一个整型的参数或字段，或者是一个期望返回 plurals 资源引用的方法（例如：android.R.plurals.ph）                      | 参数、字段、方法                                   |
| @Px                       | 用于描述一个整型的参数或字段，或者是一个期望返回像素尺寸的方法                                                             | 参数、字段、方法                                   |
| @RawRes                   | 用于描述一个整型的参数或字段，或者是一个期望返回 raw 资源引用的方法（例如：android.R.raw.ph）                              | 参数、字段、方法                                   |
| @RequiresApi              | 被注解的元素只能在被给的 API 版本或更高情况下才能被调用                                                                    | 类、接口、枚举、方法、构造器、字段                 |
| @RequiresPermission       | 被注解的元素需要或者可能需要一个或多个权限                                                                                 | 注解、方法、构造器、字段                           |
| @RequiresPermission.Read  |                                                                                                                            |                                                    |
| @RequiresPermission.Write |                                                                                                                            |                                                    |
| @RestrictTo               | 被注解的元素只能在特定的范围内被访问                                                                                       | 注解、类、接口、枚举、方法、构造器、字段、包       |
| @Size                     | 被注解的元素需要提供其大小或长度                                                                                           | 参数、字段、方法、注解                             |
| @StringDef                | 用于描述一个注解，然后再用这个被描述的注解去描述一个 string 类型的参数或字段，或者是一个期望返回值是显示声明常量之一的方法 | 注解                                               |
| @StringRes                | 用于描述一个整型的参数或字段，或者是一个期望返回 string 资源引用的方法（例如：android.R.string.ok）                        | 参数、字段、方法                                   |
| @StyleableRes             | 用于描述一个整型的参数或字段，或者是一个期望返回 styleable 资源引用的方法（例如：android.R.styleable.TextView_text）       | 方法、参数、字段                                   |
| @StyleRes                 | 用于描述一个整型的参数或字段，或者是一个期望返回 style 资源引用的方法（例如：android.R.style.TextAppearance）              | 方法、参数、字段                                   |
| @TransitionRes            | 用于描述一个整型的参数或字段，或者是一个期望返回 transitionRes 资源引用的方法（例如：android.R.transition.fade）           | 方法、参数、字段                                   |
| @UiThread                 | 被注解的元素只能在主线程被调用                                                                                             | 方法、构造器、类、接口、枚举                       |
| @WorkerThread             | 被注解的元素只能在工作线程被调用                                                                                           | 方法、构造器、类、接口、枚举                       |
| @XmlRes                   | 用于描述一个整型的参数或字段，或者是一个期望返回 xml 资源引用的方法（例如：android.R.xml.test）                            | 参数、字段、方法                                   |
| @SuppressLint             | Lint 静态检测工具将会忽略被注解元素的警告                                                                                  | 类、字段、方法、参数、构造器                       |
| @TargetApi                | Lint 静态检测工具将会以指定 API 版本对待被注解元素                                                                         | 类、接口、枚举、方法、构造器                       |

 