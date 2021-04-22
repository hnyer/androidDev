# Groovy

## groovy 简介
```text
Groovy 是一种基于 JVM 的敏捷开发语言, 
可以与 Java 完美结合，而且可以使用 Java 所有的库

Groovy 在语法上构建除了非常多的语法糖，
许多在 Java 层需要写的代码，在 Groovy 中是可以省略的。 
既可以作为编程语言也可以作为脚本语言
```

## Groovy 开发环境配置
```text
1、配置JDK
Groovy 依赖于Java ，所以需要 JDK。

2、配置 Groovy 库
https://groovy.apache.org/download.html
下载、解压。

3、测试环境
命令行输入 groovy -v ，如果打印 Groovy版本 说明配置成功。

4、自带编辑器 (不推荐)
使用 groovyconsole 命令会启动自带的编辑器。
按 CTRL + R 运行。

5、IntelliJ IDEA编辑 (推荐)
IntelliJ IDEA 默认支持 Groovy ，无需额外安装插件。
```

## 基本数据类型
```text
byte  、short  、int  、long  、
float  double  、char  、Boolean 、String  
```


## 其他数据类型
```text
Byte 、Short
Integer 、Long
Float 、Double

java.math.BigInteger
java.math.BigDecimal
```
 


## 循环
```text
while 、for 、for-in 

int[] array = [0,1,2,3];
for(int i in array) {
    println(i);
}
```

## 定义变量
```text
不用像Java那样指定明确类型，会自动判断类型。
def a = 100;
println(a);

def d = "HelloWorld";
println(d);
```


## 函数、方法
```text
参数可以设置默认值
static int sum(int a,int b = 5) {
    int c = a+b;
    return c;
}
```


 

## Groovy 闭包
```text
// 无参闭包
def clos = { param -> println "Hello  ${param}"};
clos.call("World");

// 带参闭包
def str1 = "Hello";
def clos1 = {println "${str1}  ${it}"};
clos1.call("World");

// 闭包可以作为函数的参数
display(clos);

// 数组使用闭包
def lst = [11, 12, 13, 14];
lst.each {println it}

// 字典使用闭包
def mp = ["TopicName" : "Maps",  "TopicDescription" : "Methods in Maps"]
mp.each {println it}
mp.each {println "${it.key} maps to: ${it.value}"}

def static display(clos) {
    clos.call("aivin");
}
```


## 相等判断
```text
== 相当于 Java 的 equals，
如果需要比较两个对象是否是同一个，需要使用 .is()。
``` 