# APP的启动流程
https://www.jianshu.com/p/a72c5ccbd150

专业名词|作用
-|-
zygote进程|在系统发出请求时，负责分裂出其它的进程。
SystemServer进程|android系统里面重要的服务都是在这个进程里面开启的
ActivityManagerService(AMS)|负责系统中所有Activity的生命周期。
Instrumentation|完成对Application和Activity初始化和生命周期的工具类
