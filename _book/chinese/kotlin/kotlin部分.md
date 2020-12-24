# kotlin
Kotlin 是 JetBrains 开发的基于 JVM 的语言。完全兼容java。

## DSL（domain specific language）
领域专用语言：专门解决某一特定问题的计算机语言，比如大家耳熟能详的 SQL 和正则表达式。

## 相关 库、工具
```text
// Anko
以往的布局都是要从 XML 中解析出来。
而 Anko 则是直接创建 View，用代码构建布局，省去了解析 XML 的时间。

// ktor
由 Kotlin 团队打造的 Web 框架 ，可以用来编写服务器。

Kotlin Android 扩展 
https://www.kotlincn.net/docs/tutorials/android-plugin.html
findViewById() 。尽管存在一系列的开源库能够为这个问题带来解决方案。
现在 Kotlin 安卓扩展插件能够提供与这些开源库功能相同的体验，不需要添加任何额外代码。
apply plugin: 'kotlin-android-extensions' //扩展插件
import kotlinx.android.synthetic.main.＜布局＞.*   //关联xml
```

## 访问范围
```text
private,
protected  //子类可见
internal // 同一模块
public
```

## 数据类型
```text
Byte、Short、Int、Long、Float、Double 、Boolean 、String
```

## 变量 、常量
```text
var value : Int = 10   // 变量
val name  : String ="abcdedfdfa"   // 常量
```

## 类型转换
```text
var info : String = "100"
var info2 :Int = info.toInt()
var info3 :String = info2.toString()

将y转为String类型 ，如果转换失败就会返回null 。
val x: String? = y as? String

// 转换失败就会抛出异常
val x: String? = y as String
```
 

## 位运算符
```text
shl  —— 类似Java的<<
shr  —— 类似Java的>>
ushr —— 类似Java的>>>
and  —— 类似Java的&
or   —— 类似Java的|
xor  ——  同Java中的按位异或 ^
inv  —— Java中的按位取反 -
```

## 函数
```text
// fun ,有参数 ，有返回值
fun sum(a: Int, b: Int): String {
    return (a + b).toString()
}

// 无返回值
fun showInfo(a: Int, b: Int){
}

// vararg是关键字 可变参数 
fun vars(vararg v:Int){
    for(vt in v){
        //...
    }
}
```

## 字符串模板
```text
val age  :Int = 20
var info :String ="年龄是$age"
//复杂格式
var info2 = "$info 字符串长度是${info.length}"
```

## 集合
```text
// List
val list: List<Any> = listOf<Any>(1, "2", 3)

// Array 
val faces = Array(5) {  "a"   } // Array初始化，大小5，初始值为"a"

// Set 
val set: Set<Any> = setOf<Any>(1, "2", 3, "3")

// Map 
val map: Map<String, Any> = mapOf("k1" to "v1" , "k2" to 3)

indices

//collection.joinToString(xxx)
对集合中的元素进行简单的拼接

// collection.any(XXX)
Returns `true` if at least one element matches the given [predicate].
```


## 空指针  
```text
//?  表示可为null
var ageString : String ?
//?.    为空就返回null ，不是空 就返回 对应的值
ageString =student ?. name

// ?:  ageString 为空就用默认值 ，否则用取得的值
var info :String?
info =  ageString ?:  "21"

// !!  非空断言 ageString不为空时获取对应的值 ，否则直接报错 。
val length : Int  = ageString!!.length

// 判断是否为空
var flag : Boolean = ageString.isNullOrBlank()
```

## let
```text
常与非空判断符?. 一起使用
mSurfaceTexture?.let {
    // compoundDrawables 不为空就执行...
    // it 是指 mSurfaceTexture
   mCamera?.setSurfaceTexture(it)  
}
```

## @标签
```text
## return@
return@XXlabel 语法用于指定此语句从几个嵌套函数中返回哪个函数。 
```




## 对象
所有的类都继承自 Any 。
默认所有的类都是final类型 。
```text
// (info: String) 是主构方法
open  class Person  (info: String){
   // 可以被重写
   open var info : String ="人类"
   var age1 : Int = 25

   // 次构造函数
  constructor(info: String  ,age: Int) : this(info) {
       this. info = info
       this.age1 = age
   }
   // open 普通函数   ,可以被重新
   open fun showInfo(){
   }

   // 普通函数 不可以被重新
   fun showInfo2(){
   }
}

public class Student(info: String) : Person(info) {
    override var info : String =info

    // 次构造函数
    constructor(info: String  ,age: Int) : this(info) {
        this. info = info
        this.age1 = age
    }

    // override 重写函数
    override fun showInfo() {
        super.showInfo()
    }

}

// 测试
var student1 : Student = Student("新人类"  ,19)
var student2 : Student = Student("新人类"   )
```

##  类初始化
```text
kotlin增加了一个新的关键字init用来处理类的初始化问题，
init模块中的内容可以直接使用构造函数的参数。
```
## 继承
Kotlin 中所有类都继承该 Any类，Any是所有类的超类。
如果一个类要被继承，需要用open 关键字进行修饰。

## 函数重写、属性重写
在基类中，使用fun声明函数时，默认不能被子类重写。如果需要被子类重写。 
需要用open修饰它, 然后子类重写方法使用 override 说明 。


## 接口
```text
interface  Language  {
    fun chinease(msg :String )
    fun english(msg :String )
}

// Person 是父类 ， Language是接口
public class Student(info: String) : Person(info),Language{
    override fun english(msg: String) {
    }

    override fun chinease(msg: String) {
    }
    ....
}
```


## apply
```text
调用对象的apply函数，在函数范围内，
可以任意调用该对象的任意方法，并返回该对象。
```

##  with
```text
将对象作为函数的参数，在函数内可以通过 this指代该对象。
返回值为函数的最后一行或return表达式
```


## when
```text
表达式类似 java 中的 switch .
可以用when替换复杂的if/else语句 。
在when中，else同switch的default。这是你给出的当你的表达式没有覆盖情况下的解决方案。
```


## intArrayOf
```text
创建一个int数组
```



## 循环
```text
for(i in 0..9){
    // [0,9]
}
 
(0 until 9){
    // [0,9)
}
```


## mapTo
```text
// mapTo 与循环搭配使用。此处的解释是：
// [0 ,size)中循环取索引i ，新建BeanB对象，并将这些对象全部添加到集合 listA中 
(0 until mTitles.size)
        .mapTo( listA) {
                   BeanB( array1[it],  array2[it] ) 
        }
```


## 三目表达式
kotlin不支持。 请直接使用 if else来代替



##  data
data class  ,  在 Kotlin 中，不需要自己动手去写一个 JavaBean，
可以直接使用 DataClass，使用 DataClass 编译器会默默地帮我们生成以下函数 。 



## 双冒号 ::  
```text
// 调用d对象的方法getResult
d::getResult
```


## 原生字符串 三引号
```text
A raw string is delimited by a triple quote (""" xxx """),
 contains no escaping and can contain newlines and any other characters.
val str:String= """ the price is$\d 199 """
```





## 拓展函数
如果一个类没有定义某个功能，可以在类外（具体使用的地方）写一个拓展函数。
拓展函数名可以跟类的原有函数名字相同，但是会优先使用类的原有函数。
空对象也可以使用拓展函数。 this指的是被改造的那个对象是例。
```text
class User(var name:String)

/**扩展函数**/
fun User.Print(){
    print("用户名 $name")
}

fun main(arg:Array<String>){
    var user = User("Runoob")
    user.Print()
}
```

## 伴生对象
Kotlin移除了static的概念。
通常用 object 和 companion object  来实现
```text
使用"object"修饰静态类
被修饰的类，可以使用类名.方法名的形式调用
var version_name1 = Util.getName()
object Util {
    fun getName(): String {
        return BuildConfig.VERSION_NAME
    }
}

使用"companion object"修饰静态方法
可以使用类名.方法名的形式调用
var version_name2 = Util2.getName()
class Util2 {
    companion object {
        fun getName(): String {
            return BuildConfig.VERSION_NAME
        }
    }
}
```


## 数据类
kotlin的数据类 类似java中手动创建的 javaBean ，用来传递数据 。
用关键字 data 申明。 数据类不能继承其他类（可以实现其他接口） 。
在使用数据类时，与普通类无异 。


## 密封类
用关键字 sealed 申明。
密封类就像一个更强大的枚举。密封类只有固定子类 。

## 枚举
```text
enum class Color{
    RED,BLACK
}

枚举可以被初始化
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00)
}
```


## 挂起函数
```text
用 Suspend 修饰
等xx函数执行完才会输入某个结果。 但是线程并不会阻塞。
类似回调的使用
```


## 协程
```text
可以用协程替换Thread的作用。
协程可以看作是一个轻量级的线程，
它不是由操作系统或是虚拟机来实现的，而是通过编译器。
这意味着相对于线程，协程的开销更小。
```



## 命名参数 、 默认参数
```text
函数定义：第一个参数没有默认值，后面的都有默认值
fun reformat(str: String,  
            normalizeCase: Boolean = true,
            upperCaseFirstLetter: Boolean = true,
            divideByCamelHumps: Boolean = false,  
            wordSeparator: String= "bbb")
{
  // ....
}

//不会引起歧义的时候调用 (全部使用默认值)
reformat("tom")
//有歧义时，必须要指定参数名字。 指定命名。否则不知道你要将值赋给谁
reformat("tom" , upperCaseFirstLetter= false)
```



## kotlin main 入口函数
```text
fun main(args: Array<String>){
    println("hello kotlin")
}
```

## java 调用 kotlin
```text
// Companion 是关键字 ， newInstance 是kotlin中的静态方法
mFragments[FIRST] = DeviceFragment.Companion.newInstance();
```

## kotlin 调用 java
```text
val javaDemo =  JavaDemo() //新建对象
val msg =javaDemo.helloMsg // 调用对象的方法
println("msg="+ msg)
```

##  kotlin 调用 jni 
external表示将一个声明标记为不是在 Kotlin 中实现.
```text
init
{
    System.loadLibrary("joke-lib")
}

external fun getWXAPPID(): String
``` 

## init
kotlin增加了一个新的关键字 init 用来处理类的初始化问题，
init模块中的内容可以直接使用构造函数的参数。


## 对象表达式
对象表达式，是一种代替Java中的匿名内部类的方法。
使用object关键字来进行编写。比匿名内部类更先进。
可以实现多个接口。
```text
// 关键  object : 指明
tab_layout.setOnTabSelectListener( object : OnTabSelectListener {
        //  设置监听函数
})
```

## 监听函数
```text
private val onGestureListener = object : RecordButton.OnGestureListener {
    override fun onUp() {   }
}
```


## 新建对象
java中新建对象要用到new 关键字。 kotlin中没有new关键字。
直接写类名 即可。


## get set 方法
kotlin中可以直接使用对象的属性，不用调用 get和set方法。 
```text 
//这是FragmentActivity.java中的源码
public FragmentManager getSupportFragmentManager() {
    return mFragments.getSupportFragmentManager();
}
为了获得一个 FragmentManager 实例，
java中需要这样写  this.getSupportFragmentManager() , 
而kotlin中可以直接这样使用 this.supportFragmentManager
注意 getSupportFragmentManager 和getSupportFragmentManager 之间的特点。（符合set和get的命名规范的）

//var 默认有get set方法。不写就用默认的。
var heiht: Float = 145.4f
        private set  // 说明这个set方法是私有的

// 重写了get方法
private val isViewAttached: Boolean
    get() = mRootView != null
```
 
 
 ## 延迟初始化 、 懒加载
 lateinit 和 lazy  、notNull
 ```text 
1、lateinit 、notNull 只用于var，lazy只用val

2、lateinit 不能用在可空的属性上和 基本类型上。
让编译期忽略对属性未初始化的检查，后续在哪里以及何时初始化还需要开发者自己决定。
（让编译器别BB，我知道这个变量一定要初始化，别老是提醒我）

3、lazy 用到的时候才会调用，生成过程只会执行一次，
多次调用用到的是第一次生成的结果 。

private val linearLayoutManager by lazy {
    LinearLayoutManager(activity, LinearLayoutManager.VERTICAL, false)
}

4、一般情况使用lateinit修饰符，最为优雅。
当类型是原生类型，或者为局部变量时，只能只用notNull委托。
var context: Context by Delegates.notNull()
 ```
 



## 委托 by
Kotlin 直接支持委托模式 。  
```text 
委托属性也是一种 约定 。
setValue 和 getValue都需带有 operator 关键字修饰。
operator fun getValue(thisRef: Any?, property: KProperty<*>): T {
    return xx
}

operator fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
    //
}
```


## 星号 * 
```text 
将数组展开并传入可变参数
val arr = intArrayOf(2, 1, 3)
sumTest( * arr) // 星号的意思是将数组中的值取出来作为可变参数
        
private fun sumTest( vararg values :Int ){
// 参数是可变参数 
}
```


## 泛型
跟java的泛型差不多。参考java即可。

 
## return@
return@XXlabel 语法用于指定此语句从几个嵌套函数中返回哪个函数。  