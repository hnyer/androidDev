# Android字节码插桩
## 什么是插桩
```text
插桩就是将一段代码插入或者替换原本的代码。
字节码插桩顾名思义就是在我们编写的源码编译成字节码（Class）后，
在Android下生成dex之前修改Class文件，
修改或者增强原有代码逻辑的操作。
```

## 插桩的时机
```text
字节码操作框架的作用在于生成或者修改Class文件，
因此在Android中字节码框架本身是不需要打包进入APK的，
只有其生成/修改之后的Class才需要打包进入APK中。
它的工作时机在Android打包流程中的 生成Class之后，打包dex之前。
```


## 字节码插桩框架 ASM
The source code is hosted on Gitlab. 
https://gitlab.ow2.org/asm/asm 

```text
asm 是直接使用指令来控制字节码。
所以 asm 的执行效率很高。



```