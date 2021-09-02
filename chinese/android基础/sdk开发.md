# SDK开发
将一些业务逻辑独立出来，打包成jar、so、aar，暴露一些APIs给外部调用，也可以称为SDK。
SDK 不同于普通应用,不能频繁的进行更新,以免让开发者觉得 SDK 不稳定或者让开发者频繁的集成 。
为了安全起见,数据加密类、模块算法类都都应该采用NDK开发 。

## sdk加密方案

方案名字|缺点 |优点
-|-
代码混淆|1、有些类不能混淆（例如工具类，自定义控件等，如果被混淆了用户就不方便调用），所以对代码架构要求比较高。<br>2、混淆后的代码虽然不易识别了，但是仔细分析还是能看懂的。|
修改class的字段||某些字段对运行没有影响，但是能导致别人无法反编译。
自定义类加载器ClassLoader|ClassLoader容易被攻破|
jvmti|不适用于Android平台？（我暂时还未测试成功）|
 

## SDK 版本号命名及修改原则
SDK版本号命名和我们以往的命名规则并无太大不同,通由4部分组成,格式为:
```text
V主版本号_子版本号_阶段版本号_日期版本号_希腊字母版本号
比如：V1_1_2_161209_beta.
```

### 希腊字母版本号说明
```text
1、Alpha版:内部测试版。此版本表示该软件在该阶段主要是以实现功能为主,Bug相对较多,需要继续修改,通常只在内部流通流通而不对外开放。
2、Beta版:外部测试版。该版本相对Alpha已经有了很大的改进,不存在严重的Bug,但还是存在一些缺陷,需要进一步的测试以检查和消除Bug。
3、RC版: 该版本已经相当成熟,不存在导致错误的Bug，与正式版相差无几。
4、Release版:该版本意味着”最终版本”,是最终交付用户或者公开发布的版本,也称为标准版。在发布的时候回以符合R来代替Release单词。
```

### 版本号修改规则
```text
1、 主版本号变化:当功能模块有较大的变化或者整体架构发生变化
2、子版本号变化:当功能有一定变化
3、阶段版本号变化:一般是Bug修复或者较小的变动,根据反馈,需要经常发布修订版本.
4、日期版本号(161209):用于记录修改项目的当前日期,每天对项目的修改都要更改日期版本号.
5、希腊字母版本号:此版本号用于标注当前软件处于那个开发阶段,当软件进入到另一个阶段是需要修改.
```

##  API版本管理
```text
原则上SDK API一旦公开发布后其状态应为不可变。
1、对于特殊情况下API的变更,需要遵守”开闭原则”
2、在需要废除某些方法时,需要在正式版发版前使用 @deprecated 标识。并给出替代方案和开始废弃的 SDK版本号。
```
 
## 经验之谈
原文 http://blog.csdn.net/dd864140130 有删减。
```text
//SDK实现目标
1、简洁易用
SDK不应该对宿主应用有过多的代码侵入,也不应该有复杂频繁的接入工作。
当我们需要使用该 SD K的服务时,通过一行代码便可启用 Ad.init(this,params)

2、稳定
① SDK的API一旦确定,如无非常严重情况不可更改。
②、必须确保SDK自身运行的稳定,并且保证接入方不会因为我们的SDK产生不稳定。
③、不能频繁更新迭代。防止增加用户负担。

//SDK整体架构设计
模块化开发
根据单一职责将系统拆分为不同的小模块，每个模块保持相对独立。
模块之间通过协议或接口通信，以减少相互之间的依赖耦合。

为了安全起见, 核心逻辑应该采用NDK开发。
   
//API版本管理
原则上SDK API一旦公开发布后，应为不可变。
对于特殊情况下API的变更,需要遵守”开闭原则”:
1、在需要调整SDK API时,优先选择添加新方法,而不是在原方法上修改 。
2、在需要废除某些方法时,需要在正式版发版前使用 @deprecated 标识,并标明开始废弃的版本。
```
 
## sdk开发-Module支持c++
```text
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


## 打包 aar
```text
// 1、生成 aar 文件：
对 library 进行 build 即可生成 AAR 。

// 2、 使用 aar
场景：将 aar 放到一个基础 library ，base_library 的libs下。
然后其他 library 都可以使用到这个aar 。

① base_library 的 build.gradle配置 
android {
    repositories {
        flatDir {
            dirs 'libs',aar_comm_path
        }
    }
}
dependencies {
    api(name: 'aar名', ext: 'aar')
}

② 整个项目下的 build.gradle 配置
allprojects {
    repositories {
        flatDir {
            dirs aar_comm_path
        }
    }
}

③ 配置其他所有依赖 base_library 并且需要使用 aar 的 library    
android {
    repositories {
        flatDir {
            dirs 'libs', aar_comm_path
        }
    }
}

④  配置自定义变量 aar_comm_path 的路径
在 gradle.properties中进行了配置
aar_comm_path=../base_library/libs
```


 