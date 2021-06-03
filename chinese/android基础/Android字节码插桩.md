# Android 字节码插桩
## 什么是插桩
```text
插桩就是将一段代码插入或者替换原本的代码。
字节码插桩顾名思义就是在我们编写的源码编译成字节码（Class）后，
在Android下生成dex之前修改Class文件，
修改或者增强原有代码逻辑的操作。
```


## 插桩的应用场景
```text
通过 代码插入、代码替换实现
无痕埋点、性能监控 、日志记录、
性能统计、异常处理、修改三方库代码等目的。
总之就是 AOP（面向切面编程）。
```


## 插桩的时机
```text
字节码操作框架的作用在于生成或者修改Class文件，
因此在Android中字节码框架本身是不需要打包进入APK的，
只有其生成/修改之后的Class才需要打包进入APK中。
它的工作时机在Android打包流程中的 java 文件生成 Class 之后，打包 .dex 之前。
```




## 字节码插桩框架 ASM
The source code is hosted on Gitlab. 
https://gitlab.ow2.org/asm/asm 

```text
在maven 库中查看最新版本，
https://search.maven.org/search?q=g:org.ow2.asm
选择 asm 和 asm-commons 
testImplementation 'org.ow2.asm:asm:9.1'
testImplementation 'org.ow2.asm:asm-commons:9.1'

asm 是直接使用指令来控制字节码。
所以 asm 的执行效率很高。

ASM提供了两种API来生成和转换已编译类，
一个是 基于事件形式的 core API ，
另一个是基于对象形式 的 tree API 。
我们一般采用占用内存较多但是难度较小的 tree API  。
```


## ASM 核心 API 和类
```text
// 对具体的 class 文件进行读取与解析
ClassReader classReader;  

// 将修改后的class文件通过文件流的方式覆盖掉原来的 class 文件，从而实现 class 修改；
ClassWriter classWriter ;

// 可以访问class文件的各个部分，比如方法、变量、注解等，用于修改 class 文件。
ClassVisitor classVisitor ; 
AdviceAdapter adviceAdapter ;//
``` 



## Transform API ( Android平台)
```text
Android Gradle 工具从 1.5.0-beta1 版本开始，包含了 Transform API，
Transform API 允许第三方插件在将编译后的类文件转换为 dex 文件之前对 .class 文件进行操作。


```