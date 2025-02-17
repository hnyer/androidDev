# kotlin
Kotlin 是 JetBrains 开发的基于 JVM 的语言。完全兼容java。

## DSL（domain specific language）
```text
领域专用语言：专门解决某一特定问题的计算机语言，比如 SQL 和正则表达式。

DSL 与系统编程语言走的是两个极端，
系统编程语言是希望解决所有的问题，
比如 Java 语言希望能做 Android 开发，又希望能做服务器开发，它具有横向扩展的特性。
而 DSL 具有纵向深入解决特定领域专有问题的特性。
DSL 的 核心思想 就是：“求专不求全，解决特定领域的问题”。
```

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

### 高阶函数- 函数作为参数
```text
fun main(args: Array<String>) {
    val info = getPeopleInfo(20, ::getName)
    println("---->${info}")
}

fun getPeopleInfo(age: Int, nameFunc: (String,String) -> String): String {
    val name = nameFunc("tom" ,"上海")
    val info = "${name}‘s age is ${age}"
    return info
}

fun getName(name: String, address: String): String {
    return "${address} ,the fuck $name"
}
// ---->上海 ,the fuck tom11‘s age is 20
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
val list1 = mutableListOf<String>()  // mutableListOf 和 ArrayList 区别不大
val list2 = ArrayList<String>()
  

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

// ?:  
var info :String?
info =  ageString ?:  "21"  // ageString 为空 info 就赋值为 21 ，不为空就赋值为 ageString

// !!  非空断言 ageString不为空时获取对应的值 ，否则直接报错 。
val length : Int  = ageString!!.length

// 判断是否为空
var flag : Boolean = ageString.isNullOrBlank()
```

## let
```text
常与非空判断符 ?. 一起使用
mSurfaceTexture?.let {
    // compoundDrawables 不为空就执行...
    // it 是指 mSurfaceTexture
   mCamera?.setSurfaceTexture(it)  
}
```


## 非空判断、为空判断
```text
holder.id?.text = childModel.piid.ifEmpty { "N/A" }  
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


## Any 、 Unit 、Nothing
```text
Unit 类似于 Java中 的 Void 。
一切方法/函数都是表达式，表达式是总是有值的，所以每一个方法都必有一个返回值。
如果没有用 return 明确的指定，那么一般来说就会用自动帮我们加上 Unit .

Nothing 在Java中并没有一个相似的概念。
Nothing 类型表达式计算结果永远是不会反回的。
//因为 pick 永远不会反回值，而是直接抛出了异常，这个时候可以用 Nothing 作为 pick 函数的返回值
fun pick(index: Int): Nothing {
    throw Exception()
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
1、作用1 ，方便设置对象的参数
2、作用2，如果括号里是一个值时，就是返回值
```


## when
```text
表达式类似 java 中的 switch .
可以用when替换复杂的if/else语句 。
在when中，else同switch的default。这是你给出的当你的表达式没有覆盖情况下的解决方案。
```


## intArrayOf
```text
创建一个int数组 ，同时赋值
val arr = intArrayOf(1, 2 ,3) 
```

## IntArray
```text
创建一个int数组 ，没有初始值
 var firstPostions = IntArray(3) ;
```


## takeIf
```text
val postion =
findFirstPosition().takeIf { it != RecyclerView.NO_POSITION } ?: 10

findFirstPosition 的返回结果(假如为A) 等于  RecyclerView.NO_POSITION  ，
postion最后的值 就是 A ，否则就是 10 。
```


## takeUnless
```text
与 takeIf 相反
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


##  return break continue
```text
fun main(args: Array<String>) {
//    myBreakDemo(3)
//    myContinueDemo(3)
    myReturnDemo(3)
}

// break
fun myBreakDemo(index: Int) {
    val arr = intArrayOf(1, 2, 3, 4, 5, 6, 7)
    run myBreaking@{
        arr.forEach {
            if (it == index) {
                return@myBreaking
            }
            println("it=${it} ,index=${index}")
        }
    }
    println("index=${index} end---")
}
// continue
fun myContinueDemo(index: Int) {
    val arr = intArrayOf(1, 2, 3, 4, 5, 6, 7)
    arr.forEach {
        if (it == index) {
            return@forEach
        }
        println("it=${it} ,index=${index}")
    }
    println("index=${index} end---")
}
// return
fun myReturnDemo(index: Int) {
    val arr = intArrayOf(1, 2, 3, 4, 5, 6, 7)
    arr.forEach {
        if (it == index) {
            return
        }
        println("it=${it} ,index=${index}")
    }
    println("index=${index} end---")
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
Kotlin移除了static 的概念。
通常用 object 和 companion object  来实现
```text
被 object 修饰的类，可以使用类名.方法名的形式调用
var version_name1 = Util.getName() //
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

## 回调函数
```text
函数的参数，最后一个参数是lambda时，为了美观要写在括号外面，
否IDE 会提示 Lambda argument should be moved out of parentheses 

// 以下这两种写法是等价的
val cc = SingletonHolder<String, String>("tom",{ name: String, age: Int ->
    "hello,fuck you"
})
val cc = SingletonHolder<String, String>("tom") { name: String, age: Int ->
    "hello,fuck you"
}
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
kotlin 的关键字 init 用来处理类的初始化问题，
init模块中的内容可以直接使用构造函数的参数。


## 对象表达式
对象表达式，是一种代替Java中的匿名内部类的方法。
使用 object 关键字来进行编写。比匿名内部类更先进。
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
java中需要这样写 this.getSupportFragmentManager() , 
而 kotlin 中可以直接这样使用 this.supportFragmentManager
注意 getSupportFragmentManager 和 getSupportFragmentManager 之间的特点。（符合set和get的命名规范的）

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


## inline 内联 , noinline
```text
内联函数起初是在 C++ 里面的。

kotlin 中 当一个函数被内联 inline 标注后，
在调用它的时候，会把这个函数方法体中的所有代码移动到调用的地方，而不是通过方法间压栈进栈的方式。

JVM 内部已经实现了内联优化，它会在任何可以通过内联来提升性能的地方将函数调用内联化，
并且与开发者手动将普通函数定义为内联相比，
通过 JVM 内联优化所生成的字节码，每个函数的实现只会出现一次，
这样在保证减少运行时开销的同时，也没有增加字节码的尺寸；
所以 inline 不适合在无参数的函数中(会有黄色的提示)。

inline 能带来的性能提升，往往是在参数是 lambda 的函数上。
因为如果参数是 lambda 却没有使用 inline ,
在编译过程中，因为 lambda 参数 多出来的类，会增加内存的分配。

noinline 用来区部取消内联
inline fun mainInline(func1: () -> Unit, noinline func2: () -> Unit) {
    func1()
    func2()
}
```


## 注解
```text
@JvmOverloads
如果没有这个注解 ，
Java 代码不能调用在 Kotlin 中使用默认值实现的重载函数或构造函数。

fun f(a: String, b: Int= 0, c: String="abc"){ }
// 相当于 
void f(String a, int b, String c){ }

@JvmOverloads fun f(a: String, b: Int= 0, c:String="abc"){ }
// 相当于同时对应下面三个函数
void f(String a)
void f(String a, int b)
void f(String a, int b, String c)
```


## 单例
```text
class XfAsrReslutTool private constructor() {
    companion object {
        fun getInstance(): XfAsrReslutTool {
            return SingletonFactory.INSTANCE
        }
    }
    private object SingletonFactory {
        val INSTANCE = XfAsrReslutTool()
    }
    private val asrResultCallbacks: MutableList<IXfAsrResultCallBack> = ArrayList()
    fun unregisterCallback(callback: IXfAsrResultCallBack?) {
        if (callback != null) {
            asrResultCallbacks.remove(callback)
        }
    }
    fun registerCallback(callback: IXfAsrResultCallBack?) {
        if (callback == null) {
            return
        }

        if (!asrResultCallbacks.contains(callback)) {
            asrResultCallbacks.add(callback)
        }
    }
    // 将结果分发给各个监听器
    fun dispatcherAsrReslut(result: String) {
        for (callback in asrResultCallbacks) {
            callback.onGetAsrResult(result)
        }
    }
}
```



# 协程  Coroutine
```text
协程并不是 Kotlin 提出的，概念已经提出很久了。
java 原生语法暂未支持 ,但是可以通过一些第三方的框架来实现。

对操作系统来说，线程是最小的执行单元，进程是最小的资源管理单元。
协程不是被操作系统内核所管理，是完全由程序所控制的。
一个进程可以拥有多个线程一样，一个线程可以拥有多个协程。

当系统线程较少的时候没有什么问题，当线程数量非常多的时候，却产生了问题。
一是系统线程会占用非常多的内存空间，二是过多的线程切换会占用大量的系统时间。

协程运行在线程之上，当一个协程执行完成后，可以选择主动让出，让另一个协程运行在当前线程之上。
协程并没有增加线程数量，只是在线程的基础之上通过 分时复用 的方式运行多个协程，
协程的切换在用户态完成，切换的代价比线程从用户态到内核态的代价小很多。

协程跑在单个线程内，是单线程下的并发 。不适合 CPU 密集型任务 ，适合 IO密集型任务。
此外，协程代码中决不能出现阻塞，否则整个线程都会停下来等待该操作完成。
```

## kotlin 协程 引入
https://github.com/Kotlin/kotlinx.coroutines
```text
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-core:1.4.2'
implementation 'org.jetbrains.kotlinx:kotlinx-coroutines-android:1.4.2'
val jobWork= GlobalScope.launch( Dispatchers.Main ){    }// 工作线程
val mainWork =GlobalScope.launch(Dispatchers.IO) {   }// 主线程切换
jobWork.cancel() // 取消协程
```

## 协程作用域 CoroutineScope
```text
private val coroutineScope =CoroutineScope(CoroutineName("LyOfflineModeTool") + SupervisorJob())
```

## 协程上下文 CoroutineContext
```text
暂未学习
```

## 协程异步
```text
private suspend fun hasAgileBoundByQueryDb(): Boolean {
    return suspendCoroutine { cont ->
        val resultList = mutableListOf<List<Int>>()
        coroutineScope.launch(Dispatchers.IO) {
            val agileList =
                deviceDao.getDeviceTuple4Pid(LyPidConfig.PID_AGILE_BUTTON.toString())
            if (agileList.isEmpty()) {
                cont.resume(false)
                return@launch
            }
            cont.resume(true)
        }
    }
}
```
