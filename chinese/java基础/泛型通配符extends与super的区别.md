#### 泛型的作用

```xml
泛型，就是将类型参数化，
就是在定义的时候你不知道这个地方需要什么类型的数据 ，只有实际使用的时候才会知道 。
让编译器在编译期间帮忙检查类型错误。
```

###### 未使用泛型：
Object是所有类的根类，任何类的对象都可以设置给该Object引用变量，使用的时候可能需要类型强制转换 。(程序员手动做的强制转换是否正确 是要等到运行时才会发现，这样就晚了！！)
![img](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801180942_osChina_未使用泛型.png)
![img](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801180942_osChina_未使用泛型2.png)

###### 使用了泛型：
这样设计的容器在使用时编译器就可以帮忙做很大一部分的类型安全检查工作了，这就避免了很多运行时的ClassCastException异常，程序员也无需记住各种对象的类型和担心类型匹配问题了。
 （编译器在编译是就帮我们做检查，更容易发现错误！！）
![img](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801180942_osChina_使用了泛型.png)



<br><br>

##### 定义泛型常用的字母
原则上来讲，使用任何字母或字符串都是可以的 。
但是为了代码的可读性，我们一般有如下 约定；
```xml
//在集合中使用，因为集合中存放的是元素)
E - Element  

// 类型
T - Type

//键
K - Key  

// 值
V - Value

// 数字
N - Number  

//表示不确定的java类型
? -             
```

泛型的应用范围 | 说明
-|-
泛型类|
泛型接口|
泛型方法|


#### 泛型类
```java
// 申明 泛型 T ， 不申明 ，无法使用 。（也可以将T写成其他乱七八糟的字母）
public class Tool <T > {
	private T value ;
}
```
<br>

#### 泛型接口
```java
public interface Person <T>{
   T getType();
}}
```

<br>

#### 泛型方法
```java
// <T200> 说明当前是一个 泛型方法 ， 并且将类型定义为  T200
// 返回值 为 T200
public <T200>  T200  showKeyName (List<T200> list) {
      return list.get(0) ;
  }


public class Tool <T200 > {
// 静态方法不能使用类定义的泛型。
public static void show(T200 t){
 // 这样使用编译器会报错
}
}

```

<br>

#### 泛型与 可变参数
```java
//printMsg("111",222,"aaaa","2323.4",55.55);
public static <T300> void printMsg( T300... args){
    for(T300 t : args){
      System.out.println("t is " + t);

    }
}
```


<br><br>


## 泛型中 extends 和 super的区别

[参考资料](https://itimetraveler.github.io/2016/12/27/%E3%80%90Java%E3%80%91%E6%B3%9B%E5%9E%8B%E4%B8%AD%20extends%20%E5%92%8C%20super%20%E7%9A%84%E5%8C%BA%E5%88%AB%EF%BC%9F/)


 <?extends T>：是指 “上界通配符（Upper Bounds Wildcards）”

```Java
Plate<? extends Fruit>
```

翻译成人话就是：一个能放水果以及一切是水果派生类的盘子。再直白点就是：啥水果都能放的盘子。

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801181014_osChina_上界通配符.png)


 <?super T>：是指 “下界通配符（Lower Bounds Wildcards）”

```Java
Plate<？ super Fruit>
```

一个能放水果以及一切是水果基类的盘子。

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801181014_osChina_下界通配符.png)


##### 为什么要用通配符和边界

优点： 让Java不同泛型之间的转换更容易了。 使用泛型、通配符提高了代码的复用性。

缺点： 容器的部分功能失效。

（从类型转换的角度去理解 ，编译器不能确定具体类型，但是要确保不报错，所以不能给你强制转换）

1、上界<? extends T>不能往里存，只能往外取


2、下界<? super T>不影响往里存，但往外取只能放在Object对象里
























-
