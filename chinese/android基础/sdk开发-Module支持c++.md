# sdk开发-Module支持c++

```xml
1、按照正常流程创建一个支持c++的项目。
此时 CMakeLists.txt 文件在 app 这个项目里面。

2、新建一个module ，并添加到app中。

3、将 CMakeLists.txt 剪切到 module对应的 目录下。

4、 同理 ，以下代码也剪切到 module对应的文件中去。

externalNativeBuild {
    cmake {
        path "CMakeLists.txt"
    }
}

externalNativeBuild {
    cmake {
        cppFlags "-std=c++11 -frtti -fexceptions"
    }
}

5、刷新工程即可。
```
