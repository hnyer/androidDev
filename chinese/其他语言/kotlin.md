# kotlin
Kotlin 是 JetBrains 开发的基于JVM的语言。

## 数据类型
```java
Byte、Short、Int、Long、Float、Double 、Boolean 、String
```

## 变量 、常量
```java
// 变量
var value : Int = 10
// 常量
val name :String ="aivin"
```

## 函数
```java
// fun ,有参数 ，有返回值
fun sum(a: Int, b: Int): String {
    return (a + b).toString()
}

// 无返回值
fun showInfo(a: Int, b: Int){
}

// vararg 可变长参数
fun vars(vararg v:Int){
    for(vt in v){
        //...
    }
}
```

## 字符串模板
```java
val age :Int = 20
var info :String ="年龄是$age"
//复杂格式
var info2 = "$info 字符串长度是${info.length}"
```

## 空指针  
kotlin只是在编译的时候加强了空指针的检查，最后还是要判断是否为空才能最终使用。卵用。
```java
//?表示可为null
var ageString : String ?
//  ?.  student 为空就返回null ，不是空 就返回 对应的值
// 只能赋值给 可以为空的变量
ageString =student ?. name

// ?:  ageString 为空就用默认值 ，否则用取得的值
var info :String?
info =  ageString ?:  "21"

// !!  非空断言 ageString不为空时获取对应的值 ，否则直接报错 。
// 不建议使用 这种方式
val length : Int  = ageString!!.length

// 判断是否为空
var flag : Boolean = ageString.isNullOrBlank()
```

## 类型转换
```java
var info : String = "100"
var info2 :Int = info.toInt()
var info3 :String = info2.toString()
```


## 对象
所有的类都继承自 Any 。
默认所有的类都是final类型 。
```java
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
   // 普通函数 open  ,可以被重新
   open fun showInfo(){
   }

   // 普通函数 open  ,不可以被重新
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


## 接口
```java
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


# 安卓使用 kotlin
## 类初始化
```java
kotlin增加了一个新的关键字init用来处理类的初始化问题，
init模块中的内容可以直接使用构造函数的参数。
```

## apply
```java
调用对象的apply函数，在函数范围内，可以任意调用该对象的任意方法，并返回该对象。
```

## with
```java
将对象作为函数的参数，在函数内可以通过 this指代该对象。
返回值为函数的最后一行或return表达式
```


## when
```java
表达式类似 java 中的 switch
```



## intArrayOf
```java
创建一个int数组
```

## 位运算符
```java
shl  —— 类似Java的<<
shr  —— 类似Java的>>
ushr —— 类似Java的>>>
and  —— 类似Java的&
or   —— 类似Java的|
xor  ——  同Java中的按位异或 ^
inv  —— Java中的按位取反 -
```


## companion object
```java
Kotlin语言中使用"object"修饰静态类
Kotlin语言中使用"companion object"修饰静态方法
```


## let
```java
常与非空判断符一起使用
compoundDrawables?.let {
  //compoundDrawables不为空就执行...
}
```


## 双冒号 ::  
```java
// 调用d对象的方法getResult
d::getResult
```
