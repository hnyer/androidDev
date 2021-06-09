# NDK 交叉编译
就是在一个平台上生成另一个平台上的可执行代码。 例如在window平台下编译生成Apk 。
## 动态库
```text
.so  、 .dll
```

## 静态库
```text
.a  、 .lib  
将所依赖的文件都编译到同一个文件中。体积大。
```




## window下 MinGW 模拟 linux编译环境
[MinGW](http://www.mingw.org/)(Minimalist GNUfor Windows)  可以在window下模拟linux环境。当你没有linux环境或者在linux下遇到奇葩问题的时候，可以用这个替代一下。
```text
1、将默认的那些编译器等都安装好。
2、然后点击  xxx\msys\1.0\msys.bat 即可使用
```

# 传统编译方式
## build 常用命令
命令 | 作用
-|-
ndk-build |编译
ndk-build V=1 |编译并打印出它所执行的详细编译命令
ndk-build NDK_DEBUG=1 | 编译为调试版本
ndk-build NDK_DEBUG=0 | 编译为 正式版本
ndk-build NDK_OUT=./mydir|指定编译生成的文件的存放位置
ndk-build -B|强制重新编译已经编译完成的代码
ndk-build clean|清除所有编译出来的临时文件和目标文件

## Android.mk 模板
android_mk [ 官方参考](https://developer.android.google.cn/ndk/guides/android_mk.html#over)


## Android.mk 配置示例
```text
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_C_INCLUDES += $(LOCAL_PATH)/../libffmpeg  \
    $(LOCAL_PATH)/hardCodec

LOCAL_MODULE := libhiesproto
ALL_DEFAULT_INSTALLED_MODULES += $(LOCAL_MODULE)
LOCAL_STATIC_LIBRARIES := libyuv2rgb_neon
LOCAL_SRC_FILES += Hies_proto.c \
    comm_utils.c \
    mbuffer/HiMbuffer.c

include $(BUILD_STATIC_LIBRARY)

include $(CLEAR_VARS)
LOCAL_MODULE := libh265decoder
LOCAL_SRC_FILES := hardCodec/H265/lib/libHW_H265dec_Andr.a
include $(PREBUILT_STATIC_LIBRARY)

include $(CLEAR_VARS)
LOCAL_CFLAGS := -D__STDC_CONSTANT_MACROS -DHI_OS_ANDROID -DHI_MEDIA_CODEC_DECODING

WITH_ANDROID_VECTOR := true
include $(LOCAL_PATH)/../utils.mk
LOCAL_C_INCLUDES += \
    $(LOCAL_PATH)/../libffmpeg \
    $(LOCAL_PATH)/../arm_neon \
    $(LOCAL_PATH)/../jni \

LOCAL_SRC_FILES := \
    mediaplayer.cpp \
    nativeWindow.cpp \
    mbuffer/HiMbuffer.c \

RTSP_PROTO_INC := \
    $(LOCAL_PATH)/hardCodec/protocol/HiRtspClient \
    $(LOCAL_PATH)/hardCodec/protocol/HiRtspClient/rtspclient/include \
    $(LOCAL_PATH)/hardCodec/protocol/HiRtspClient/rtspclient/src

RTSP_PROTO_SRC := $(wildcard $(LOCAL_PATH)/hardCodec/protocol/HiRtspClient/rtspclient/src/*.c)

LOCAL_C_INCLUDES += $(RTSP_PROTO_INC)
LOCAL_SRC_FILES += $(patsubst $(LOCAL_PATH)/%, %, $(RTSP_PROTO_SRC))
LOCAL_PRELINK_MODULE := false
LOCAL_SHARED_LIBRARIES := libffmpeg libmpp libvpu
LOCAL_STATIC_LIBRARIES :=libHWAHStreaming libyuv2rgb_neon libhiesproto libh265decoder

LOCAL_LDLIBS += -llog -lz -lm -ldl
LOCAL_MODULE := libahplayer
ALL_DEFAULT_INSTALLED_MODULES += $(LOCAL_MODULE)
include $(BUILD_STATIC_LIBRARY)
```



## Android.mk 部分解释
### LOCAL_PATH := $(call my-dir)
```text
表示源文件在开发树中的位置。
在这里，构建系统提供的宏函数 my-dir 将返回当前目录（即Android.mk文件所在的目录）的路径。
```


### include $(CLEAR_VARS)
```text
CLEAR_VARS 变量
其值由构建系统提供。指向特殊 GNU Makefile，可为您清除许多 LOCAL_XXX 变量，
例如 LOCAL_MODULE、LOCAL_SRC_FILES 和 LOCAL_STATIC_LIBRARIES。
请注意，它不会清除 LOCAL_PATH。此变量必须保留其值，因为系统在单一 GNU Make 执行环境（其中所有变量都是全局的）中解析所有构建控制文件。
在描述每个模块之前，必须声明（重新声明）此变量。
```

###  LOCAL_MODULE := hello-jni
```text
LOCAL_MODULE 变量
将存储您要构建的模块的名称。每个模块名称必须唯一，且不含任何空格。
```



###  LOCAL_SRC_FILES := hello-jni.c
```text
最后一行帮助系统将所有内容连接到一起。
include $(BUILD_SHARED_LIBRARY)

BUILD_SHARED_LIBRARY 变量
指向 GNU Makefile 脚本，用于收集您自最近 include 后在 LOCAL_XXX 变量中定义的所有信息。
```



## 变量和宏
```text
系统提供许多可用于 Android.mk 文件中的变量。
除了这些变量之外，您还可以定义自己的任意变量。
为了便于辨认，自定义变量建议在名称前附加 MY_。
```

### 系统自带的变量

NDK 定义的变量 | 含义
-|-
CLEAR_VARS| 取消定义LOCAL_XXX 变量。
BUILD_SHARED_LIBRARY|构建动态库
BUILD_STATIC_LIBRARY|构建静态库
PREBUILT_SHARED_LIBRARY|引用另一个模块中的预建库
PREBUILT_STATIC_LIBRARY|
TARGET_ARCH| CPU 架构的名称
TARGET_ARCH_ABI|使用空格作为多个目标之间的分隔符
TARGET_PLATFORM|作为构建系统目标的 Android API 级别号
TARGET_ABI|
LOCAL_PATH|指定当前文件的路径
LOCAL_MODULE|模块的名称
LOCAL_MODULE_FILENAME|强制改变.so的文件名
LOCAL_SRC_FILES|源文件列表
LOCAL_CPP_EXTENSION|为 C++ 源文件指明 .cpp 以外的文件扩展名
LOCAL_CPP_FEATURES|指明您的代码依赖于特定 C++ 功能
LOCAL_C_INCLUDES|指定相对于 NDK root 目录的路径列表
LOCAL_CFLAGS|设置在构建 C 和 C++ 源文件时要传递的编译器标志。
LOCAL_CPPFLAGS|
LOCAL_STATIC_LIBRARIES|存储当前模块依赖的静态库模块列表
LOCAL_SHARED_LIBRARIES|依赖的共享库模块列表
LOCAL_WHOLE_STATIC_LIBRARIES|表示链接器应将相关的库模块视为整个存档
LOCAL_LDLIBS|其他链接器标志列表
LOCAL_LDFLAGS|其他链接器标志列表
LOCAL_ALLOW_UNDEFINED_SYMBOLS|捕获“未定义的符号”错误
LOCAL_ARM_MODE|
LOCAL_ARM_NEON|
LOCAL_DISABLE_NO_EXECUTE|
LOCAL_DISABLE_RELRO|
LOCAL_DISABLE_FORMAT_STRING_CHECKS|
LOCAL_EXPORT_CFLAGS|
LOCAL_EXPORT_CPPFLAGS|
LOCAL_EXPORT_C_INCLUDES|
LOCAL_EXPORT_LDFLAGS|
LOCAL_EXPORT_LDLIBS|
LOCAL_SHORT_COMMANDS|
LOCAL_THIN_ARCHIVE|
LOCAL_FILTER_ASM|

### 系统自带的函数宏

NDK 提供的函数宏 | 含义
-|-
my-dir|返回最后包含的 makefile 的路径，通常是当前 Android.mk 的目录
all-subdir-makefiles|返回位于当前 my-dir 路径所有子目录中的 Android.mk 文件列表
this-makefile|返回当前 makefile 的路径
parent-makefile|返回包含树中父 makefile 的路径
grand-parent-makefile|返回包含树中祖父 makefile 的路径
import-module| 按模块的名称查找和包含模块的 Android.mk  


## 其他
```text
if [ $? -eq 0 ]
$?是shell变量,表示"最后一次执行命令"的退出状态.0为成功,非0为失败.
```


## Application.mk模板
[Application.mk官方参考资料](https://developer.android.google.cn/ndk/guides/application_mk.html)
```text
Application.mk不是必须的。如果没有找到，就会执行一套自己的默认规则。
自定义的Application.mk文件 放在project/jni/目录下 （project代表你所写程序的项目目录），这样ndk-build命令可以自动搜索到它。  
```

## Application.mk 配置的作用和范围
```text
1、指明 目标平台的ABI类型  
2、指明 工程是 release/debug模式
3、指明 C++标准库类型
4、指定 Toolchains(工具链)
```


##  Application.mk 简单示例
```text
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE = libhellos
LOCAL_CFLAGS = $(L_CFLAGS)
LOCAL_SRC_FILES = hellos.c
LOCAL_C_INCLUDES = $(INCLUDES)
LOCAL_SHARED_LIBRARIES := libcutils
LOCAL_COPY_HEADERS_TO := libhellos
LOCAL_COPY_HEADERS := hellos.h
include $(BUILD_STATIC_LIBRARY)
#编译动态库
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE = libhellod
LOCAL_CFLAGS = $(L_CFLAGS)
LOCAL_SRC_FILES = hellod.c
LOCAL_C_INCLUDES = $(INCLUDES)
LOCAL_SHARED_LIBRARIES := libcutils
LOCAL_COPY_HEADERS_TO := libhellod
LOCAL_COPY_HEADERS := hellod.h
include $(BUILD_SHARED_LIBRARY)
#使用静态库
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := hellos
LOCAL_STATIC_LIBRARIES := libhellos
LOCAL_SHARED_LIBRARIES :=
LOCAL_LDLIBS += -ldl
LOCAL_CFLAGS := $(L_CFLAGS)
LOCAL_SRC_FILES := mains.c
LOCAL_C_INCLUDES := $(INCLUDES)
include $(BUILD_EXECUTABLE)
#使用动态库
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := hellod
LOCAL_MODULE_TAGS := debug
LOCAL_SHARED_LIBRARIES := libc libcutils libhellod
LOCAL_LDLIBS += -ldl
LOCAL_CFLAGS := $(L_CFLAGS)
LOCAL_SRC_FILES := maind.c
LOCAL_C_INCLUDES := $(INCLUDES)
include $(BUILD_EXECUTABLE)
```


## 系统自带的变量
NDK 定义的变量 | 含义
-|-
APP_PROJECT_PATH|指定应用项目根目录的绝对路径。 Application.mk 文件放如果在 $PROJECT/jni/ 下，此变量可选。
APP_OPTIM|默认release
APP_CFLAGS|为任何模块编译任何 C 或 C++ 源代码时传递到编译器的一组 C 编译器标志
APP_CPPFLAGS|在仅构建 C++ 源文件时传递到编译器的一组 C++ 编译器标志
APP_LDFLAGS|构建系统在链接应用时传递的一组链接器标志。
APP_BUILD_SCRIPT|指定Android.mk的默认路径
APP_ABI|
APP_PLATFORM|包含目标 Android 平台的名称
APP_STL|
APP_SHORT_COMMANDS|
NDK_TOOLCHAIN_VERSION|定义为 4.9 或 4.8 以选择 GCC 编译器的版本
APP_PIE|
APP_THIN_ARCHIVE|





# cmake 编译方式
Android 对 CMake的说明请参考 [CMake API ](https://developer.android.com/ndk/guides/cmake.html#variables)  、 [CMake官网](https://cmake.org/)
```text
androidStudio 在2.2之前对 C/C++ 支持不是很友好，没有语法提示，配置编译也不方便 。
从androidStudio 2.2正式版开始 ，采用了cmake方式编译，简化了编译配置 。
可以不用再手动输入 javac、javah命令等。
```

## CMake
Android Studio 默认使用 CMake 编译原生库，如果你只打算用ndk-build来编译的话，你就不需要这个组件。CMake是AndroidStudio2.2提供的构建C项目的工具

## LLDB
使用它来调试本地代码。



## androidstudio2.2+ 输出c代码log信息
```html
1、 error: undefined reference to '__android_log_print'
app层的 gradle 中 defaultConfig 标签下 进行配置
ndk {
    //就是依赖ndk工具包中的一个liblog.so文件
ldLibs "log"
}
2、cmake中对log的配置使用默认的即可 。

3、//导入log包
#include <android/log.h>
//定义log标签，名字随意
#define LOG_TAG  "C_TAG"
//定义日志级别
#define LOGD(...)  __android_log_print  (ANDROID_LOG_DEBUG, LOG_TAG, __VA_ARGS__)
//打印log
jint value = 1000 ;
LOGD("value=%d", value);
LOGD("value=%d", 100);

4、有些手机能输出Java中的log，却不能输出c代码中的log 。
原因可能跟手机的设置有关系。 在开发者选项中进行相关设置即可。
```


##  build.gradle 部分配置
```text
// 指明项目中需要用到的 第三方的 .so , .a 文件
sourceSets {
    main {
        // 如果不配置，android就默认使用这个路径
        jniLibs.srcDirs = ['src/main/jniLibs']
        // 如果喜欢放 libs目录，可以直接这样写
        //jniLibs.srcDirs = ['libs']
    }
}

ndk{
    // 指明只编译的对应的cpu类型
    abiFilters "armeabi-v7a"
}

cmake {
    cppFlags "-std=c++11 -frtti -fexceptions"
    // 让CMake构建原生库时支持“NEON”,默认的是不支持
    arguments "-DANDROID_ARM_NEON=TRUE"
}
```

```text
packagingOptions  // 用来对 gradle做一些配置
exclude      //过滤掉某些文件或者目录不添加到APK中 
pickFirst   // 匹配到多个相同文件，只提取第一个 。
merge       // 匹配的文件都添加到APK中，和pickFirst有些相反，会合并所有文件。
doNotStrip  //设置某些动态库不被优化压缩。
```


## CMakeLists.txt 部分配置
```text
#这里设定工程路径  ， 等同于  CMAKE_SOURCE_DIR
# set(PROGRAM_PATH "E:/test-fffffmpeg/WkNative/app")

# 批量导入rtsp 相关代码
file( GLOB_RECURSE wkrtsp_cpp "src/main/cpp/rtspclient/src/*.cpp")
file( GLOB_RECURSE wkrtsp_c "src/main/cpp/rtspclient/src/*.c")
add_library(wkrtsp-lib
             SHARED
             ${wkrtsp_c}
             ${wkrtsp_cpp})

#导入已经编译好的opencv库-头文件
include_directories( src/main/cpp/OpenCV/include )
# 导入 opencv 的so
add_library(libopencv_world
            SHARED
            IMPORTED )
set_target_properties( libopencv_world
                       PROPERTIES
                       IMPORTED_LOCATION
                       ${CMAKE_SOURCE_DIR}/src/main/jniLibs/armeabi-v7a/libopencv_world.so
                       )

```

## CMakeLists.txt 说明
```text
# 添加了c++11的支持
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")
# 此标志在链接成共享库时有效
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wall -v -Wl,--no-warn-shared-textrel")

# 系统库，打印日志
find_library( log-lib  log )
# 系统库 ，压缩库
find_library( z-lib  z )
# 数学计算
find_library( m-lib  m )
```

## 其他配置
```text
enable_language(ASM) //支持汇编
configure_file () // 代码文件中可以使用CMake中的变量
set( KEY, value)  // 键值对,  设置KEY的值为value。获取方式为 ${KEY} 
unset () //取消键值对设置
list ()  // 用来做一些操作，例如LENGTH 、APPEND 、FIND 、REMOVE_AT 等
add_definitions () // 增加编译参数
add_custom_command () // 添加自定义命令
add_subdirectory () // 将指定的文件夹加到build任务列表中。
install ()  // 用于定义安装规则，安装的内容可以包括目标二进制、动态库、静态库以及  文件、目录、脚本等
include_directories () //导入头文件
add_library () //添加一个库
add_executable () // 引入一个可执行文件
set_target_properties () // 将各种 so 文件的路径转化成简单的值
find_library () // 寻找本地存在的库的路径,例如日志打印库log
link_directories ()    // 添加动态连接库的路径
target_link_libraries ()// 将目标文件与库文件进行链接.
target_include_directories ()  // 也是用来配置头文件
target_compile_options () // 指定编译选项
file(GLOB xxx "${PATH_TO_MEDIACORE}/camera/*.cc")   // file函数，自动将指定目录下的符合后缀的文件加入到xxx变量中，GLOB/GLOB_RECURSE  是一种加入策略， 
project (XXX)  // 这个项目的名字叫xxx
message ([<mode>] "message to display" ...) //输出打印信息 ， mode= none / STATUS /WARNING /FATAL_ERROR 等
add_executable (xxx  bbb.cpp)   //为 xxx这个 project 添加一个可执行的文件bbb.cpp
option(<variable> "<help_text>" [value]) //变量名 + 描述信息 + 初始值(只能是ON或OFF)
if 、foreach、while //  条件判断
macro (xx  bb) // 开始宏定义
endmacro () // 结束宏定义
function () // 开始函数定义
endfunction () //结束函数定义

CMAKE_SOURCE_DIR  // 内置常量 ， 表示当前项目根目录
CMAKE_CURRENT_LIST_DIR // 内置常量 ， 当前文件所在的目录
PROJECT_SOURCE_DIR   // 内置常量 ，表示的是源代码的路径。
PROJECT_BINARY_DIR   //内置常量 ，表示的是cmake build 的路径
ANDROID_PLATFORM_LEVEL //系统版本
ANDROID_ABI   // 设备cpu架构类型
STRLESS、STRGREATER、STREQUAL    // 字符串比较
LESS、GREATER、EQUAL  //数字比较
CMAKE_BUILD_TYPE  //cmake的编译方式 ，调试版还是正式版 Debug ，Release
PROPERTIES  //用来指定库的一些编译属性
IMPORTED_LOCATION  // 库的地址
CMAKE_SYSTEM  // 系统全名 
CMAKE_SYSTEM_NAME //系统名称 
CMAKE_SYSTEM_VERSION // 系统版本
CMAKE_SYSTEM_PROCESSOR  //CPU名称
MATCHES  // 用正则表达式去匹配到给定的内容
```

# jni
基于 Androidstudio 、cmake 、 c/c++  。注意：jni层去读取SD卡的文件，需要在AndroidManifest.xml配置权限。 



## JNI 使用场景
```text
1、提高代码安全性。因为.so文件反编译困难。（加密一些算法等）
2、方便使用已经存在的c/c++库。可以加载 .dll 和 .so 格式的动态库。
3、提高某些特定情况下的执行效率。（c/c++的效率比java高。不过并不能明显提升android程序的性能）
```

## JNI语法
```text
#ifdef __cplusplus
extern "C"
#endif
// 如果是c++代码，也统一用c的语法去编译。
#ifdef __cplusplus
extern "}"
#endif


extern "C" JNIEXPORT void JNICALL
Java_com_xxx_nPlay(JNIEnv *env, jobject instance, jstring url_) { 
 ...
}

extern "C"
JNIEXPORT void JNICALL
Java_com_kgdwbb_jnistudy_MainActivity_helloJNI(JNIEnv* env, jobject thiz,jstring msg) {
    //do something
}
extern "C"  // 代码是用C++语言写的就需要用这个，  C语言编写则不需要。
JNIEXPORT   // 标记为该方法可以被外部调用，类似Java的public函数和private函数的区别。
JNICALL     // 说明这个函数是一个JNI函数，用来和普通的C/C++函数进行区别。
```

### JNIEnv 
```text
JNIEnv是 JavaVM 在线程中的代表, 每个线程都有一个, JNI 中可能有很多个 JNIEnv;
每个JNIEnv 都是线程专有的, 其它线程不能使用本线程中的 JNIEnv, 线程 A 不能调用 线程 B 的 JNIEnv;
但是，一个本地方法可被不同的 Java 线程所调用，因此可以接受不同的 JNIEnv。

//JNIEnv 作用
1、调用Java函数
JNIEnv代表Java运行环境,可以使用JNIEnv调用Java中的代码;

2、操作Java对象
Java对象传入JNI层就是Jobject对象, 需要使用JNIEnv来操作这个Java对象;
```
 
## JNI 动态注册 、静态注册
```text
无论是动静态注册还是动态注册，
Java端代码都是一样的，
区别在于 C/C++里面的代码形式

// 静态注册，简单易懂，Androidstudio 自动生成就采用这种方式
extern "C" JNIEXPORT jstring JNICALL
Java_com_mirkowu_jintest_jni_JniTest_stringFromJNI(JNIEnv *env, jclass clazz) {
    std::string hello = "Hello from C++  by 静态注册";
    return env->NewStringUTF(hello.c_str());
}

// 动态注册，事先维护好一张表，将 Java和 native 端的方法名对应起来，效率相对高一点
static JNINativeMethod method_table[] = {
        // 第一个参数a 是java native方法名，
        // 第二个参数 是native方法参数,括号里面是传入参的类型，外边的是返回值类型，
        // 第三个参数 是c/c++方法参数,括号里面是返回值类型，
        {"a", "()Ljava/lang/String;",                                     (jstring *) aaa},
        {"b", "(Ljava/lang/Object;Ljava/lang/String;)Ljava/lang/String;", (jstring *) getStringWithDynamicReg},

};
``` 
 
 

## JNI的数据类型

java基本类型  | JNI类型 | bit |-|java基本类型  | JNI类型 | bit
-|-|-|-|-|-|-
boolean|jboolean|8 |  |int    |jint    |32
byte   |jbyte   |8 |  |long   |jlong   |64
char   |jchar   |16|  |float  |jfloat  |32
short  |jshort  |16|  |double |jdouble |64
void   |void    |无|  |       |        |  

java引用类型 | JNI类型 |- | java引用类型 | JNI类型 
-|-|-|-|-
Object    | jobject     | |short[ ]  |jshortArray 
Class     | jclass      | |int[ ]    |jintArray   
String    |jstring      | |long[ ]   |jlongArray  
Object[ ] |jobjectArray | |float[ ]  |jfloatArray 
boolean[ ]|jbooleanArray| |double[ ] |jdoubleArray
byte[ ]   |jbyteArray   | |Throwable |jthrowable  
char[ ]   |jcharArray   | |          |

## 字符串
```text
std::string helloCpp = "Hello from C++ , aivin";
showLog("--> %s" ,helloCpp.c_str()) ;
```

##  JNI签名
java中，对于某一个对象来说， 可以用过方法名和参数 就可以确定唯一的一个方法。在JNI中，通过函数名和 签名信息 可以确定唯一的方法。

### 基本数据签名的规则

java类型  | JNI类型   |  -  | java类型 | JNI类型
-|-|-|-|-
boolean | Z |   | long   | J
byte    | B |   | float  | F
char    | C |   | double | D
short   | S |   | void   | V
int     | I |   |        |

### 签名详解
```text
// 类的签名（注意末尾的分号）
L+包名+类名+；
java.lang.String  ->   Ljava/lang/String;

// 对象签名
跟它所属的类的签名一样。

// 一维数组签名
格式： [类型
double[ ]  ->  [ D

// 多维数组签名
格式：  n个 [   + 类型签名
int[ ] [ ]   ->  [[I

// 方法签名   (跟方法名无关)
格式： （参数类型签名） + 返回值类型签名
boolean fun( int a , double b , int[ ] c)   ->     (ID[I)Z
void fun(int i)  -> (I)V
```

## 引用类型
```text
//全局引用
全局引用可以跨方法、跨线程使用，直到被显式释放。
NewGlobalRef() //新建全局变量
DeleteGlobalRef() //删除全局引用
 
//局部引用
局部引用仅在创建它的native函数及该函数调用的函数中有效。局部引用将在该函数返回时被释放。
DeleteLocalRef() //删除引用

// 弱全局引用
与全局引用类似，弱引用可以跨方法、线程使用。但弱引用不会阻止GC回收它引用的对象，
它所引用的对象可能是不存在的或者已经被回收。在内存不足时可能会被收回了。使用前先判断一下。
global_weakRef_str= (jstring) env->NewWeakGlobalRef(str2);
if(! env->IsSameObject(NULL , global_weakRef_str)){
    showLog("this global_weakRef_str is usefully") ;
}
env->DeleteWeakGlobalRef(global_weakRef_str) ;
``` 

##  Java 和 C 函数的映射表
```text
// { java中函数名字 , 参数和返回值 , 对应的c函数}
{
  "setUsbLinkVideoData",
  "(Ljava/nio/ByteBuffer;I)V",
	(void*)com_media_ffmpeg_FFMpegPlayer_setUsbLinkVideoData
}
```

## 常用函数
函数名 |说明
-|-
FindClass | 
CallIntMethod |
CallVoidMethod |
GetStaticMethodID |
CallStaticIntMethod|
NewObject |
CallStaticObjectMethod |
NewStringUTF |
GetStringUTFChars| 得到一个UTF-8编码的字符串
GetStringChars| 得到UTF-16编码的字符串
ReleaseStringUTFChars|
JNI_OnLoad |so库加载时调用。 <br>如果你的*.so档没有提供JNI_OnLoad()函数，VM会默认使用最老的JNI 1.1版本。
JNI_OnUnload | -
SetByteArrayRegion|将本地的数组数据拷贝到了 Java 端的数组中
env->ThrowNew |抛出异常信息 <br> jclass exceptionClazz = env->FindClass("java/lang/RuntimeException"); <br> env->ThrowNew(exceptionClazz, "自错误信息");
reinterpret_cast | 类型强制转换<br>MyParcel* parcel = reinterpret_cast <MyParcel*>(nativePtr);
GetStaticFieldID|
SetStaticFieldID|



```text
GetBooleanField( )  // 返回对象的实例(非静态)域的值
GetByteField( )
GetCharField( )
GetShortField( )
GetIntField( )
GetLongField( )
GetFloatField( )
GetDoubleField( )

GetObjectClass( )     // 根据 对象 获取类
GetMethodID( )        //  根据名字获取Java中指定类的指定函数
CallVoidMethod( )     //  调用返回值为void的Java函数
CallBooleanMethod ( ) // 调用返回值为boolean的Java函数
AttachCurrentThread   //当前线程获取JNI环境
DetachCurrentThread( ) //  退出当前JNI环境

GetBooleanArrayElements( )  // 获得数组指针
GetByteArrayElements( )  //
GetCharArrayElements( ) 
GetShortArrayElements( ) 
GetIntArrayElements( ) 
GetLongArrayElements( ) 
GetFloatArrayElements( ) 
GetDoubleArrayElements( ) 
```



### JNI_OnLoad(...)
```text
JavaVM* java_vm = NULL;
JNIEXPORT jint JNI_OnLoad(JavaVM* vm, void* reserved) {
    JNIEnv *env;
    if (vm->GetEnv(  (void**) &env,  JNI_VERSION_1_4) != JNI_OK)
    {
        return JNI_ERR ;
    }
    java_vm = vm;
    return JNI_VERSION_1_4;
}
```

 

## 打印日志
```text
#include <android/log.h>
#define LogTag "WK_LOGER"
#define LOGI(...) __android_log_print(ANDROID_LOG_INFO, LogTag ,__VA_ARGS__);
```

## 异常定位
```text
// androidsudio JNI崩溃 报错定位：
xxx \app\build\intermediates\cmake\debug\obj 
请进入正确的目录 , 该目录下有各种架构类型的文件夹， arm64-v8a、armeabi-v7a、x86_64等。
找到有XX.so 的文件夹。例如我的就是 arm64-v8a 里面有so。
输入命令：adb shell logcat | ndk-stack -sym arm64-v8a 。
会提示是具体哪一行代码报错，不过仍然不能像Java代码点击就会跳转的。

//cmake编译错误定位：
当CMakeLists.txt 配置文件配置错误时，会提示编译不过，错误定位方法。
CMake的错误输出不在console窗口中，而是在 本工程的 cmake_server_log.txt文件中。
打开该文件搜索error关键字，可以看到具体的错误信息。
```


## 缓存策略
直接用全局变量也可以（但是可能会被其他类的程序修改），所以建议用静态static较安全。
```text
Java_com_xxx_staticLocalCache(JNIEnv *env, jclass jclz, jstring name) { 
    static jfieldID j_fid = NULL;  //局部缓存
    if (j_fid == NULL) {
        j_fid = (*env)->GetStaticFieldID(env, jclz, "name", "Ljava/lang/String;")
    }  
}

static jfieldID j_fid = NULL;// 全局缓存
Java_com_xxx_initStaticCache  (JNIEnv *env, jclass jclz){
    j_fid = (*env)->GetStaticFieldID(env, jclz, "name", "Ljava/lang/String;");
}
// 这也叫缓存？乱七八糟。
```

## java调用native
注意文件夹路径要和native中的签名一样
```text
static {
    System.loadLibrary("music-player");
}
private native void nPlay(String url);
```

## native 调用 java
### C++主线程调用Java方法
```text
1、根据jobject获取jclass
 jclass jlz = env->GetObjectClass(instance);//jobject instance
2、获取 jmethodID
jmethodID jmid_error= jniEnv->GetMethodID(jlz, "onError", "(ILjava/lang/String;)V");
3、调用方法
jniEnv->CallVoidMethod(jobj, jmid_error, code, jmsg);// jobject,jmethodID , 参数...
```

### C++子线程调用Java方法
```text
JniEnv是线程相关的，所以子线程中不能使用创建线程的JniEnv；
JVM是进程相关的，可以通过JVM来获取当前线程的JniEnv，然后才能调用Java的方法。

JNIEnv *jniEnv;
if (javaVM->AttachCurrentThread(&jniEnv, 0) != JNI_OK)
{
    return;
}
// 如果获取成功，AttachCurrentThread 就会返回一个属于当前线程的JNIEnv指针。
jniEnv->CallVoidMethod(...);
//执行相关操作...
javaVM->DetachCurrentThread();
```


## POSIX线程
Android NDK的线程是通过POSIX标准实现的，所以也叫POSIX线程。


### POSIX 使用
```text
//头文件
#include <pthread.h>
// 创建线程锁对象
pthread_mutex_t mutex;
// 用来申明一个线程对象
pthread_t thread
//创建线程条件对象
pthread_cond_t cond; 
pthread_attr_t：线程属性
Thread-local storage（或者以Pthreads术语，称作线程特有数据）：
```

### POSIX 常用方法

常用方法| 说明
-|-
pthread_create(...)| 创建线程. <br>pthread_create(&playThreadT, NULL, threadPlay, this);<br>最后一个参数是运行函数的参数。
pthread_detach()|即主线程与子线程分离，子线程结束后，子线程资源自动回收
pthread_exit()|终止当前线程
pthread_join()|阻塞当前的线程，直到另外一个线程运行结束
pthread_attr_init()|初始化线程的属性
pthread_attr_setdetachstate()| 决定这个线程在终止时是否可以被结合
pthread_attr_getdetachstate()|获取脱离状态的属性
pthread_attr_destroy()|删除线程的属性
pthread_kill()|向线程发送一个信号
pthread_mutex_init(...) |初始化互斥锁
pthread_mutex_destroy(...) |删除互斥锁
pthread_mutex_lock(...)|占有互斥锁（阻塞操作）
pthread_mutex_trylock()|试图占有互斥锁
pthread_mutex_unlock()| 释放互斥锁
pthread_cond_init(...)|初始化条件对象
pthread_cond_destroy(...)|销毁条件对象
pthread_cond_signal(...)| 发出条件信号，<br>唤醒第一个调用pthread_cond_wait()而进入睡眠的线程
pthread_cond_wait()| 用于线程阻塞等待，直到 pthread_cond_signal 发出条件信号后才执行后续操作
pthread_key_create()| 分配用于标识进程中线程特定数据的键
pthread_setspecific()| 为指定线程特定数据键设置线程特定绑定
pthread_getspecific()| 获取调用线程的键绑定，并将该绑定存储在 value 指向的位置中
pthread_key_delete()| 销毁现有线程特定数据键
pthread_attr_getschedparam()|获取线程优先级
pthread_attr_setschedparam()|设置线程优先级
pthread_equal()| 对两个线程的线程标识号进行比较
pthread_self()|查询线程自身线程标识号
pthread_cancel()| 在安卓NDK中已经被删除。因为比较危险。


#### pthread_create(...)

```text
pthread_create(&pthread,NULL,threadCallBack,NULL);
其总共接收4个参数，
第一个参数为pthread_t对象，
第二个参数为线程的一些属性我们一般传NULL就行，
第三个参数为线程执行的函数，
第四个参数是传递给线程的参数

```

<br>
#### pthread_exit(...)
```text
pthread_exit(&thread) ;
参数也可以传NULL。
```


## java与native共享内存
大概原理，在native开辟一块内存。将这块内存的首地址传给java层，以后的相关操作就通过这个地址获得这块内存，用native代码直接操作内存。
```text
暂时用得少，以后再补充。
android中的应用有 Parcel，因为直接操作内存的，所以比操作IO的Serializable 效率高。
```
 

## 其他
```text
RegisterNatives(clazz, methods, size)   // 将clazz类中的 所有方法methods全部注册为本地方法。
jlong id= reinterpret_cast<jlong>(record);  // c++ 对象与对象句柄进行互相转换
auto *record = reinterpret_cast<CameraRecord *>(id);
```



## jni Demo
[jniSample](https://gitee.com/AivinPrivate/Android_Jni_cpp) 演示效果：

- [x] java调用c++
- [x] c++回调Java函数
- [x] c++获取并修改Java静态变量
- [x] Java开启子线程让c++执行耗时操作，并更新Java ui
- [x] 传递并修改数组
- [x] c++创建Java定义的对象并返回
- [ ] c++调用第三方库实现加密解密
- [ ] c++读取文件
- [ ] c++接收Java传递的控件 并绘制图像
- [x] c++获取apk包名
- [x] c++获取apk签名 哈希值 (签名校验)


## JNI函数签名生成工具
```text
public class SignatureGen
{
	private  static final HashMap<String,String> Primitives = new HashMap<String, String>();

	static
	{
		Primitives.put(Void.class.getName(),"V");
		Primitives.put(Boolean.class.getName(),"Z");
		Primitives.put(Byte.class.getName(),"B");
		Primitives.put(Character.class.getName(),"C");
		Primitives.put(Short.class.getName(),"S");
		Primitives.put(Integer.class.getName(),"I");
		Primitives.put(Long.class.getName(),"J");
		Primitives.put(Float.class.getName(),"F");
		Primitives.put(Double.class.getName(),"D");				
	}

	/**
	 * @param 返回值的类型
	 * @param params 参数类型
	 * @return 签名
	 */
	public static String getSignature( Class ret, Class...params )
	{
		StringBuilder builder = new StringBuilder();
		builder.append("(");
		for( Class param : params ) {
			builder.append(getSignature(param));
		}		
		builder.append(")");
		builder.append(getSignature(ret));
		return builder.toString();
	}

	private static String getSignature( Class  param )
	{
		StringBuilder builder = new StringBuilder();
		String name = "";
		if( param.isArray() )
		{
			name = param.getComponentType().getName();
			builder.append("[");
		}else {
			name = param.getName();				
		}

		if( Primitives.containsKey(name) )
		{
			builder.append(Primitives.get(name));
		}
		else {			
			builder.append("L"+name.replace(".","/")+";");
		}

		return builder.toString();
	}

}

//使用方法
//第一个参数是返回值类型 ，后面的参数是参数类型
例如需要获得这个函数的签名：
int func(boolean a,String b)
//参数应该这样填写
SignatureGen.getSignature(Integer.class,Boolean.class,String.class));
//获得签名如下
(ZLjava/lang/String;)I
```

## POSIX 简单示例 

```text 
#include <jni.h>
#include "wkplayer.h"
#include <pthread.h>
#include <unistd.h>
JavaVM* java_vm = NULL;
pthread_mutex_t mutex;
// 全局 回调对象
jobject callback = NULL;
// 全局回调函数
jmethodID gOnNativeMessage = NULL;

//传递pthread参数用的结构体
struct NativeWorkerArgs
{
    jint id;
    char  reslut[20];
};

extern "C"
JNIEXPORT void JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_FFmpegPlayer_playMyMedia(JNIEnv *env, jobject instance, jstring url_)
{
    const char *url = env->GetStringUTFChars(  url_, 0);
    LOGI("url:%s", url);
    env->ReleaseStringUTFChars( url_, url);
    // 在逻辑需要的时候开启子线程，并在子线程中回调java函数
    pthread_t  handle ;
    //传到子线程中的参数
    NativeWorkerArgs* nativeWorkArgs = new NativeWorkerArgs();
    nativeWorkArgs->id = 100;
    strcpy(nativeWorkArgs->reslut , "登录成功！");
    //创建线程 0 代表成功
    int result = pthread_create(&handle, NULL, nativeWorkerThread, (void*) nativeWorkArgs);
}

//子线程 执行的函数
void* nativeWorkerThread(void* args)
{
    JNIEnv* env = NULL;

    if ( 0 == java_vm->AttachCurrentThread(&env, NULL))
    {
        //lock
        if (0 != pthread_mutex_lock(&mutex))
        {
            jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
            env->ThrowNew(exceptionClazz, "Unable to lock mutex--");
        }
        NativeWorkerArgs* nativeWorkerAgrs = (NativeWorkerArgs*) args;
        // 模拟延时 ，3秒
        sleep(2);
        LOGI("参数=== %d , %s  " ,nativeWorkerAgrs->id ,nativeWorkerAgrs->reslut);
        env->CallVoidMethod(  callback, gOnNativeMessage, nativeWorkerAgrs->id , env->NewStringUTF(nativeWorkerAgrs->reslut)   );
        delete nativeWorkerAgrs;
        java_vm->DetachCurrentThread();

        if (0 != pthread_mutex_unlock(&mutex))
        {
            jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
            env->ThrowNew(exceptionClazz, "Unable to unlock mutex--");
        }
    }
    return NULL;
}

extern "C"
JNIEXPORT void JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_FFmpegPlayer_setNativeCallback(JNIEnv *env,
                                                                        jobject instance,
                                                                        jobject callback0)
{

    if (NULL == callback)
    {
        callback = env->NewGlobalRef(callback0);
    }

    //初始java回调
    if (NULL == gOnNativeMessage)
    {
        jclass clazz = env->GetObjectClass(callback);
        gOnNativeMessage = env->GetMethodID(clazz, "callbackForNative", "(ILjava/lang/String;)V");
    }

  }

extern "C"
JNIEXPORT void JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_FFmpegPlayer_initNative(JNIEnv *env, jobject instance)
{
    //初始化互斥量
    if (0 != pthread_mutex_init(&mutex, NULL))
    {
        jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
        env->ThrowNew(exceptionClazz, "Unable to init mutex");
    }

}

extern "C"
JNIEXPORT void JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_FFmpegPlayer_freeNative(JNIEnv *env, jobject instance)
{
    if (NULL != callback)
    {
        env->DeleteGlobalRef(callback);
        callback = NULL;
    }

    //释放互斥量
    if (0 != pthread_mutex_destroy(&mutex))
    {
        jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
        env->ThrowNew(exceptionClazz, "Unable to destroy mutex--");
    }

}
```



## TCP socket 简单示例
记得在子线程中进程网络耗时操作。
~~用 ByteBuffer 方式测试失败。还没找到原因。~~

```text
// java端定义接口
public native boolean createSocket();
public native boolean connectSocket(String ip, int port);
public native int recvSocket(byte[] buffer, int offset, int count);
public native boolean sendStringData(String sendData);
public native boolean sendBytesData(byte[] byteBuffer);
public native boolean closeSocket();
```

<br>

```text
#include <jni.h>
#include<stdio.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>

JavaVM* java_vm = NULL;
int sockId;
// 创建socket
extern "C"
JNIEXPORT jboolean JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_TcpClinet_createSocket(JNIEnv *env, jobject instance)
{
    sockId = socket(AF_INET, SOCK_STREAM, 0) ;
    if(sockId<0)
    {
        return JNI_FALSE;
    }else{
        return JNI_TRUE;
    }
}

// 连接 socket
extern "C"
JNIEXPORT jboolean JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_TcpClinet_connectSocket(JNIEnv *env, jobject instance,
                                                                 jstring ip_, jint port)
{
    const char *ip =env ->GetStringUTFChars(  ip_, 0);
    struct sockaddr_in servaddr;
    memset(&servaddr, 0, sizeof(servaddr));
    servaddr.sin_family = AF_INET;
    servaddr.sin_port = htons(port);

    if (inet_pton(AF_INET, ip, &servaddr.sin_addr) <= 0) {
        return JNI_FALSE;
    }
    if (connect(sockId, (struct sockaddr *) &servaddr, sizeof(servaddr)) < 0) {
        return JNI_FALSE;
    }
    env->ReleaseStringUTFChars(   ip_, ip);
    return JNI_TRUE;

}

// 发送 String 类型的数据
// 发送中文 会有乱码 。
extern "C"
JNIEXPORT jboolean JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_TcpClinet_sendStringData(JNIEnv *env, jobject instance,
                                                              jstring sendData_) {
    const char *sendData = env->GetStringUTFChars(   sendData_, 0);
    if (!sockId)
    {
        jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
        env->ThrowNew(exceptionClazz, "socket已经断开");
        return JNI_FALSE;
    }
    //LOGI("发送%s=" ,sendData);
    fgets((char *) sendData, 1024, stdin);
    if (send(sockId, sendData, strlen(sendData), 0) < 0) {
        return JNI_FALSE;
    }
    env->ReleaseStringUTFChars(   sendData_, sendData);
    return JNI_TRUE;
}

// 发送 byte[] 类型的数据
extern "C"
JNIEXPORT jboolean JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_TcpClinet_sendBytesData(JNIEnv *env, jobject instance,
                                                                 jbyteArray byteBuffer_) {
    if (!sockId)
    {
        jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
        env->ThrowNew(exceptionClazz, "socket已经断开");
        return JNI_FALSE;
    }

    jbyte* pBuffer =   env-> GetByteArrayElements(byteBuffer_ , NULL);
    jint legth = env->GetArrayLength(byteBuffer_) ;

    if (send(sockId, pBuffer, legth  , 0) < 0)
    {
        return JNI_FALSE;
    }
    env->ReleaseByteArrayElements(byteBuffer_, pBuffer, 0);
    return  JNI_TRUE ;
}

// 接收byte[]信息
extern "C"
JNIEXPORT jint JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_TcpClinet_recvSocket(JNIEnv *env, jobject instance,
                                                              jbyteArray buffer_, jint offset,
                                                              jint count) {

    if (!sockId)
    {
        jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
        env->ThrowNew(exceptionClazz, "socket已经断开");
        return 0;
    }
    jbyte buf[count - offset];
    int rec_len;
    if ((rec_len = (int) recv(sockId, buf, sizeof(buf), 0)) == -1)
    {
        jclass exceptionClazz = env->FindClass("java/lang/RuntimeException");
        env->ThrowNew(exceptionClazz, "native接收异常");
        return 0;
    }

    buf[rec_len] = '\0';
    // 将本地数据拷贝到java端的数组中
    env->SetByteArrayRegion( buffer_, offset, count, buf);
    env->DeleteLocalRef(buffer_);
    return rec_len;

}

// 关闭连接
extern "C"
JNIEXPORT jboolean JNICALL
Java_com_walkera_cn_ffmpegforandoriddemo_TcpClinet_closeSocket(JNIEnv *env, jobject instance)
{
    int reslut = close(sockId);
    // 返回0 ，关闭成功
    return JNI_TRUE;

}

JNIEXPORT jint JNI_OnLoad(JavaVM* vm, void* reserved)
{
    JNIEnv *env;
    if (vm->GetEnv(  (void**) &env,  JNI_VERSION_1_4) != JNI_OK)
    {
        return JNI_ERR ;
    }
    java_vm = vm;
    return JNI_VERSION_1_4;
}
```