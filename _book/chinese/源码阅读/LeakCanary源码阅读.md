# 源码阅读
```text
1、在 Androidstudio中点击自己的代码想往下查看源码时，
throw new RuntimeException("Stub!"
这是因为没有匹配到对应版本的源码，此时按提示下载源码即可。

2、有一些源码被加了 @hide标签，从 Androidstudio中看不到，
可以从 https://github.com/anggrayudi/android-hidden-api 下载对应版本的 android.jar 替换掉。

3、替换掉 android.jar 后，Androidstudio 报错 
ERROR: Execution failed for MockableJarTransform 
这是 com.android.tools.build:gradle:x.x.x 版本的问题, 高版本的 gradle中加了一些校验机制，
https://github.com/anggrayudi/android-hidden-api/issues/46

4、最终配置
compileSdkVersion 27
targetSdkVersion 27
classpath 'com.android.tools.build:gradle:3.1.4'
```
