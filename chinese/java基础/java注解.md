# java注解

注解 是在 Java SE 5.0 版本中开始引入的概念。

#### 元注解
元注解是一种基本注解， 它能够应用到其它的注解上面。

元注解类型 | 说明| 其他
-|-|-
@Retention |说明了这个注解的的存活时| @Retention(RetentionPolicy.RUNTIME)  <br>    @Retention(RetentionPolicy.SOURCE )   <br>    @Retention(RetentionPolicy.CLASS  )
@Documented|能将注解中的元素包含到Javadoc中去|-
@Target|指定了注解运用的地方|@Target(ElementType.ANNOTATION_TYPE )  <br>  //可以给一个注解进行注解  <br>   @Target(ElementType.CONSTRUCTOR )  <br>  //可以给构造方法进行注解  <br>   @Target(ElementType.FIELD )  <br>  // 可以给属性进行注解  <br>   @Target(ElementType. LOCAL_VARIABLE)  <br>  //可以给局部变量进行注解  <br>   @Target(ElementType.METHOD )  <br>  // 可以给方法进行注解  <br>   @Target(ElementType. PACKAGE)  <br>  //可以给一个包进行注解  <br>   @Target(ElementType.PARAMETER )  <br>  //可以给一个方法内的参数进行注解   <br>   @Target(ElementType.TYPE )  <br>  //可以给一个类型进行注解，比如类、接口、枚举
@Inherited| 如果一个f类被 @Inherited 注解过的注解 进行注解的话，  <br>   那么如果它的子类没有被任何注解应用的话，  <br>   那么这个子类就继承了超类的注解。|-
@Repeatable| Java 1.8   <br>   重复注解  ， 注解的值可以同时取多个 | @Person(role="coder")  <br>   @Person(role="PM")   <br>   public class SuperMan{...

Java内置的注解|说明   | 备注
-|-|-
@Deprecated| 标记过时 | -
@Override| 标记复写| -
@SuppressWarnings| 阻止编译器报警告提示|@SuppressWarnings("all")  <br>  // 抑制所有警告  <br>   @SuppressWarnings("boxing") <br>  // 抑制装箱、拆箱操作时候的警告  <br>    @SuppressWarnings("cast") <br>  // 抑制映射相关的警告 <br>  @SuppressWarnings("dep-ann") <br>  // 抑制启用注释的警告 <br>  @SuppressWarnings("deprecation") <br>  // 抑制过期方法警告 <br>  @SuppressWarnings("fallthrough") <br>  // 抑制确在switch中缺失breaks的警告 <br>  @SuppressWarnings("finally") <br>  // 抑制finally模块没有返回的警告 <br>  @SuppressWarnings("hiding") <br>  // 忽略  关于隐藏的本地变量的警告 <br>  @SuppressWarnings("incomplete-switch") <br>  // 忽略没有完整的switch语句 <br>  @SuppressWarnings("nls") <br>  // 忽略非nls格式的字符 <br>  @SuppressWarnings("null") <br>  // 忽略对null的操作 <br>  @SuppressWarnings("rawtypes") <br>  // 使用generics时忽略没有指定相应的类型 <br>  @SuppressWarnings("restriction") <br>  // 忽略 使用不建议或者禁止的引用的警告 <br>  @SuppressWarnings("serial") <br>  // 忽略在serializable类中没有声明serialVersionUID变量 <br>  @SuppressWarnings("static-access") <br>  // 抑制不正确的静态访问方式警告 <br>  @SuppressWarnings("synthetic-access") <br>  // 抑制子类没有按最优方法访问内部类的警告 <br>  @SuppressWarnings("unchecked") <br>  // 抑制没有进行类型检查操作的警告 <br>  @SuppressWarnings("unqualified-field-access") <br>  // 抑制没有权限访问的域的警告  <br>  @SuppressWarnings("unused") <br>  // 抑制没被使用过的代码的警告
@SafeVarargs|JDK 7   <br>  阻止编译器产生 unchecked 这样的警告，效果跟 @SuppressWarnings("unchecked")相同| 注意：使用了这种注解后，开发者要确保自己的类型转换是没有问题的，否则运行时会报错的
@FunctionalInterface|Java 1.8 |当你写的接口不符合函数式接口定义的时候，编译器会报错

 <br>  
#### 注解的属性 (成员变量)
```xml
注解只有属性，没有方法。
注解的属性在注解的定义中以“无形参的方法”形式来声明，
其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。
```

 <br>  

#### 自定义注解
```xml
一般在制作自己的框架 或者某种小工具时，
我们可以使用自定义注解 。

注意： 如果仅仅自定义了注解，并使用在代码中。这些注解并不会起作用。
如果想要真正使用上它们，你还得写一个其他工具类，利用反射等原理。
```
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation {
    public int id() default -1;
    public String msg() default "Hi";
}
```

 <br>  
#### 注解与反射
```xml
通过反射获取 注解的内容。
```
```java
private void testAninotation(Class<MainActivity>  clz) throws  Exception {
         <br>  // 检测 某个类是否应用了 指定的注解
        boolean hasAnnotation = clz.isAnnotationPresent(TestAnnotation.class);
        if(hasAnnotation){
            TestAnnotation testAnnotation = clz.getAnnotation(TestAnnotation.class);
             <br>  //testAnnotation.id()+"  "+testAnnotation.msg()
        }

         <br>  // 检测 某个 属性 是否应用了 指定的注解
         <br>  // private void test( int age , String name ){...}
         <br>  ///  获取所有属性 Field[] fields = clz.getDeclaredFields();
        Field a = MainActivity.class.getDeclaredField("testMsg");
        a.setAccessible(true);
        TestAnnotation testAnnotation1 = a.getAnnotation(TestAnnotation.class);
        if ( testAnnotation1 != null ) {
             <br>  //
        }


         <br>  ///检测 某个 方法 是否应用了 指定的注解  . test 是方法名
         <br>  ///获取所有的类   Method[] method = clz.getDeclaredMethods();
        Method testMethod = clz.getDeclaredMethod("test" ,int.class ,String.class);
        if ( testMethod != null ) {
            Annotation[] ans = testMethod.getAnnotations();
            for( Annotation annotation : ans) {
                 <br>  // annotation.annotationType().getName()
                if(annotation instanceof TestAnnotation){
                    testMethod.setAccessible(true);
                     <br>  // 在反射中调用 某个类的方法
                     <br>  // 第一个参数 是类的实例 ， 以后的多个参数都是方法需要的参数
                    testMethod.invoke( new MainActivity() , 20 , "aivin") ;
                }

            }
        }

    }
```
