# AIDL
Android Interface definition language ，是一种android内部进程通信接口的描述语言。用来定义进程间的通信接口。

##  AIDL 支持的数据类型
```xml
1、Java 的基本数据类型
2、List 和 Map
元素必须是 AIDL 支持的数据类型
Server 端具体的类里则必须是 ArrayList 或者 HashMap

3、其他 AIDL 生成的接口
4、实现 Parcelable 的实体
```
