# Androidstudio 插件开发

## 环境配置
```text
1、使用 IntelliJ IDEA 进行开发 
创建 Intelli Platform Plugin 项目
创建项目时可能找不到sdk，选择 IntelliJ IDEA 的根目录即可。
 
2、如果开发的 plugin 拖放到 Androidstudio报错 
com.intellij.diagnostic.PluginException: While loading class FirApkAction: 
FirApkAction has been compiled by a more recent version of the Java Runtime (class file version 55.0), 
this version of the Java Runtime only recognizes class file versions up to 52.0 
[Plugin: com.aivin.firapkuploadplugin] [Plugin: com.aivin.firapkuploadplugin]

这是由于 IntelliJ IDEA 版本过高导致的，我用的是 IntelliJ IDEA2021，结果需要 jdk11，
但是 Androidstudio 用的是 jdk8 ，打不开 jdk11编译出来的jar包。
最后我下载安装 ideaIC-2018.3.6.win 版本即可解决。
```



## 插件源码开发
```text
1、使用 IntelliJ 社区版就可以(我用的是 ideaIC-2018.3.6.win)
注意 IntelliJ 版本如果过高，可能要求jdk11，但是很多人还在用 jdk8，
所以用 较低版本的 IntelliJ 即可。

2、创建工程类型选  Intelli Platform Plugin

3、插件UI界面的编写推荐使用插件 JFormDesigner，
这是个收费插件，网上有破解版。
插件界面用的是 Java的 swing 框架，如果你对swing 不熟悉，可以使用这个插件进行可视化开发。
另外，intelliJ 可能默认开启了自带“UI Designer”插件 ( 可视化操作 .form 文件)，
但是这个插件不好用，可以直接禁用它。

其他的一些工程配置可以直接搜索百度或者参考我这个demo
https://gitee.com/hnyer/FirApkUploadPluginX
```



## 插件打包
```text
build -> Prepare All Plugin Modules For Deployment
执行成功插件会默认在项目根目录下。
无依赖的插件是JAR包，带有依赖的插件是ZIP格式。
```
![](../pics/插件打包.png)



## 插件发布
```text
注册账号，上传到 https://plugins.jetbrains.com/
提交后一般需要两天的审核期，审核通过后可以在Androidstudio的插件中查到。

发布的时候，可能会遇到报错不让提交
Plugin has no dependencies. Please check the documentation 
解决方案是：
在plugin.xml文件中添加
<depends>com.intellij.modules.lang</depends>
```



## JFormDesigner 破解
```text
JFormDesigner 是一个第三方收费插件 ，可以可视化开发 swing 界面。
我的组合是 ideaIC-2018.3.6.win + JFormDesigner-7.0.3-intellij-idea

将 JFormDesigner\lib\JFormDesigner-Idea.jar 解压出来，然后用注册机进行注册，获得注册码。
插件文件在我的阿里云盘有保存。
```