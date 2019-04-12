# Binder详解

从英文字面上意思看， Binder 具有粘结剂的意思，那么它把什么东西粘结在一起呢？在Android系统的Binder机制中，Binder由 Client、Server、Service Manager和Binder驱动组成。Binder就是一种把这四个组件粘合在一起的粘结剂了。




### 为什么要采用Binder
Linux已经有多种跨进程通信方式，Android为何还要采用Binder？ 因为 更高效、更安全。

方式|缺点|优点|数据拷贝次数
---|---|---|---
共享内存|控制机制复杂||0
Binder||可以建立私有通道、有UID鉴别身份|1
socket|开销大、效率低||2
管道、消息队列|效率低||2


Binder 通信采用 C/S架构。

Binder框架定义了四个角色：Server、Client、ServiceManager 、驱动。

这四个角色的关系和互联网类似：Server是服务器，Client是客户终端，SMgr是域名服务器（DNS），驱动是路由器。


1、BinderDriver

尽管名叫“驱动”实际上和硬件设备没有任何关系，存在于内核空间中。  Android系统已经实现。

2、ServerManager
提供了查询服务和注册服务的功能。
Android系统已经实现。 Service Manager是一个守护进程。

3、BinderClient
调用其他进程中的相关服务。由开发者实现。


4、Binder Server
提供相关的服务。由开发者实现。

---

#### 关键类、方法
1、Native端：
IBinder 、 BBinder 、 BpBinder 、 IPCThread 、 ProcessState、 IInterface  、JavaBBinder

jni端：
android_util_Binder.cpp 、

2、Java端：
IBinder 、 Binder、  BinderProxy （Binder的一个内部类）、 Stub、 Proxy 、ServiceManager（Java源码 被@hide了）

3、Binder Driver 端：

binder_proc、 binder_thread、 binder_node 、ioctl（是一个函数）



#### 关键名词
##### 实名Binder

注册了名字的Binder。

##### Binder实体
Binder实体实际上是binder_node结构体的对象。

##### Binder引用
每一个Binder引用都是某一个Binder实体的引用。

##### 0号引用
我们可能会发现一个细节：SMgr是一个进程，Server是另一个进程，Server向SMgr注册Binder必然会涉及进程间通信。当前实现的是进程间通信却又要用到进程间通信，这就好象蛋可以孵出鸡前提却是要找只鸡来孵蛋。 Binder的实现比较巧妙：预先创造一只鸡来孵蛋：系统给Smgr创建一个特殊专门的Binder实体。 它没有名字也不需要注册。一个Server若要向SMgr注册自己Binder就必需通过0这个引用号和SMgr的Binder通信。类比网络通信，0号引用就好比域名服务器的地址。


#####  匿名 Binder
Server端可以通过已经建立（通过实名Binder实现）的Binder连接将创建的Binder实体传给Client 。由于这个Binder没有向SMgr注册名字，所以是个匿名Binder。 匿名Binder为通信双方建立一条私密通道，只要Server没有把匿名Binder发给别的进程，别的进程就无法通过穷举或猜测等任何方式获得该Binder的引用。


##### 为什么Binder只进行了一次数据拷贝？
~~mmap()分配的内存除了映射进了接收方进程里，还映射进了内核空间。所以调用copy_from_user()将数据拷贝进内核空间也相当于拷贝进了接收方的用户空间 。~~  数据从发送方的缓存区拷贝到内核的缓存区，而接收方的缓存区与内核的缓存区被是映射到同一块物理地址的，因此只需要一次拷贝即可。

先把数据拷贝到内核的缓存区，然后再从内核的缓存区拷贝给接收方。这样做会产生两次数据的拷贝。linux中的管道通信就是采取这种方式。



#### 简单理解
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231617_osChina_Binder架构示意图.png)
&nbsp;&nbsp;
&nbsp;&nbsp;
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231617_osChina_Binder通信示意图.png)

&nbsp;&nbsp;
&nbsp;&nbsp;
&nbsp;&nbsp;
&nbsp;&nbsp;
---

#### Binder 通信模型、 Binder 架构
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231557_osChina_Binder架构.jpg)

&nbsp;&nbsp;
&nbsp;&nbsp;
#### Binder 机制
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231558_osChina_Binder机制.jpg)

#### Binder 驱动
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231558_osChina_Binder驱动.jpg)

&nbsp;&nbsp;
&nbsp;&nbsp;
#### Binder 进程与线程
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231559_osChina_Binder进程与线程.jpg)

&nbsp;&nbsp;
&nbsp;&nbsp;
#### ServiceManager启动
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231559_osChina_ServiceManager启动.jpg)

&nbsp;&nbsp;
&nbsp;&nbsp;
#### ServiceManager 注册服务
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231559_osChina_ServiceManager注册服务.jpg)

&nbsp;&nbsp;
&nbsp;&nbsp;
#### ServiceManager 获取服务
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231559_osChina_ServiceManager获取服务.jpg)

&nbsp;&nbsp;
&nbsp;&nbsp;
#### 进行一次完整通讯
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802231600_osChina_Binder进行一次完整通讯.jpg)






---
#### Binder通信命令字

命令|含义|说明
---|---|---
BINDER_WRITE_READ |该命令向Binder写入或读取数据 |常用的命令
BINDER_SET_MAX_THREADS |该命令告知Binder驱动接收方线程池中最大的线程数|-
BINDER_SET_CONTEXT_MGR|将当前进程注册为SMgr。|-
BINDER_THREAD_EXIT|通知Binder驱动当前线程退出了。|-
BINDER_VERSION|获得Binder驱动的版本号|-

####  Binder写操作命令字
 命令|含义|说明
 ---|---|---
BC_TRANSACTION|Client向Server发送请求数据 |最常用
BC_REPLY|Server向Client发送回复数据 |最常用
BC_ACQUIRE_RESULT、 BC_ATTEMPT_ACQUIRE| 尚未实现|
BC_FREE_BUFFER| 释放一块映射的内存|
BC_INCREFS、BC_ACQUIRE 、BC_RELEASE 、BC_DECREFS|增加或减少Binder的引用计数 |
BC_INCREFS_DONE 、BC_ACQUIRE_DONE|处理完毕反馈信息 |
BC_REGISTER_LOOPER|通知驱动线程池中一个线程已经创建了 |
BC_ENTER_LOOPER|通知驱动该线程已经进入主循环，可以接收数据 |
BC_EXIT_LOOPER|通知驱动该线程退出主循环，不再接收数据 |
BC_REQUEST_DEATH_NOTIFICATION| 要求驱动在Binder实体销毁得到通知|
BC_DEAD_BINDER_DONE|收到销毁通知的进程在删除引用后用本命令告知驱动 |

####  Binder读操作命令字

 命令|含义|说明
---|---|---
BR_ERROR|发生内部错误|
BR_OK 、BR_NOOP|操作完成|
BR_SPAWN_LOOPER|向接收方发送该命令要求创建更多线程以备接收数据|
BR_TRANSACTION 、BR_REPLY|表示当前接收的数据是请求还是回复|
BR_ACQUIRE_RESULT 、BR_ATTEMPT_ACQUIRE 、BR_FINISHED|尚未实现|
BR_DEAD_REPLY|交互过程中如果发现对方进程或线程已经死亡则返回该消息|
BR_TRANSACTION_COMPLETE|发送数据包后，收到该消息做为成功发送的反馈|
BR_INCREFS 、BR_ACQUIRE 、BR_RELEASE 、BR_DECREFS|用于管理强/弱指针的引用计数|
BR_DEAD_BINDER 、BR_CLEAR_DEATH_NOTIFICATION_DONE|收到死亡通知书|
BR_FAILED_REPLY|如果发送非法引用号则返回该消息|


####  Binder接收/发送数据包的标准格式
成员|含义|说明
---|---|---
union <br>{ <br> &nbsp;&nbsp;&nbsp;&nbsp;size_t handle;void *ptr;<br>} target;|指明发送目的地|
void *cookie;|存放的是创建Binder实体时由该接收方自定义的任意数值，做为与Binder指针相关的额外信息存放在驱动中。|
unsigned int code;|存放收发双方约定的命令码|
unsigned int flags;|与交互相关的标志位|
pid_t sender_pid;、uid_t sender_euid;|该成员存放发送方的进程ID和用户ID，由驱动负责填入|
size_t data_size;|缓冲区存放的数据长度。|
size_t offsets_size;|偏移位置|
union { <br> struct { <br> const void *buffer; <br> const void *offsets; <br> } ptr; <br> uint8_t buf[8]; <br> } data;|放要发送或接收到的数据,|
