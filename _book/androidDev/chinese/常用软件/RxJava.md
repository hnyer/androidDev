#  [RxJava 2.x](https://github.com/ReactiveX/RxJava)
要在Android中使用RxJava2,  需要依赖 [Rxjava2.x](https://github.com/ReactiveX/RxJava) 、 [RxAndroid](https://github.com/ReactiveX/RxAndroid)

```xml
异步事件处理框架。
continued support for Java 6+ & Android 2.3+
基于观察者设计模式。

RxJava1.x 跟 RxJava2.x 不能共存 ，会冲突。
```

## RxJava组成元素
RxJava基本组成元素 | 说明
-|-
被观察者 | 数据源  发送数据
观察者  |  接收到（处理后的数据）
建立订阅关系 |  
操作符 | 可以对数据进行过滤、转换等处理
线程切换 |  指定发送和接收的线程


## RxJava 常用类

常用类 | 作用
-|-
Observable| 被观察者 <br>
ObservableEmitter| 用来发出事件的，它可以发出三种类型的事件  <br> x.onNext() <br> x.onComplete() <br> x.onError() <br> 发射规则如下： <br>  1、上游可以发送无限个onNext ，下游也可以接收无限个onNext   <br><br> 2、当上游发送了一个onComplete后（只能发一次）, 上游onComplete之后的事件将会继续发送, 而下游收到onComplete事件之后将不再继续接收事件  <br><br> 3、当上游发送了一个onError后（只能发一次）, 上游onError之后的事件将继续发送, 而下游收到onError事件之后将不再继续接收事件  <br><br> 4、上游可以不发送onComplete或onError.  <br><br> 5、onComplete 和 onError 只能发送一个 , 不能都发送
ObservableOnSubscribe |
Observer| 观察者
Disposable| x.dispose() 切断订阅关系。 <br>  调用dispose()后 ，观察者接收不到数据，但是数据源还可以继续发送
CompositeDisposable| Disposable 容器 <br> x.add(...)  <br> x.clear()
Flowable | Rxjava2 新增的 。数据发送 。我们可以用Flowable + Subscriber 来解决 收发速度不一致问题
Subscriber| 与Flowable搭配， 数据接收。
Subscription|  切断订阅关系 <br> Subscription.cancel()  <br>  观察者请求数据源发送多少个数据 <br> subscription.request(Long.MAX_VALUE)
FlowableEmitter | 返回 观察者 能处理多少个数据 <br>flowableEmitter.requested()
Action |
BiConsumer |
BiFunction |
Predicate|
BiPredicate|
BooleanSupplier|
Cancellable|
Consumer |
Function |
Function3|
Function4|
Function5|
Function6|
Function7|
Function8|
Function9|
IntFunction|
LongConsumer|
Schedulers | 调度器。 <br>  当数据源和观察者在同一个线程时，这是一个同步的订阅关系 。 <br>  数据源每发送一个事件后必须等到观察者接收处理完了以后才能接着发送下一个。  <br>  当处于不同线程时 ,就是异步的订阅关系, 这个时候数据源可以自由发送数据。  <br>//CPU计算密集型线程 默认线程数等于处理器的数量  <br>  Schedulers.computation()   <br>  // 使用指定的Executor作为调度器 <br>  Schedulers.from()  <br>  // io操作的线程 <br>  Schedulers.io()  <br>  // 常规新线程 <br>  Schedulers.newThread()  <br>  // Android主线程 <br>  AndroidSchedulers.mainThread()  <br>  // 在当前线程执行 ，不过需要等队列中的其他任务完成后才执行 <br>  Schedulers.trampoline()  <br>  
BackpressureStrategy| 背压策略 抗压力策略  <br>   背压是指在异步场景中，数据源发送事件速度远快于观察者的处理速度的情况下， <br> 一种告诉 数据源 需要降低发送速度的策略 。  <br>    // 缓存区大小128，超过大小继续发就会报错 <br>      BackpressureStrategy.ERROR   <br>   // 默认128，超过大小继续发就会 提示缓冲满了 <br>   BackpressureStrategy.MISSING   <br>   // 默认大小128 ，大于128时就会修改为无限大。直到OOM <br>   BackpressureStrategy.BUFFER    <br>   // 默认128 ，超过128后来的数据会被丢掉 <br>   BackpressureStrategy.DROP   <br>   // 默认128 ，超过128就会只会保留最后的一个数据。  最后总个数 是 129 <br>   BackpressureStrategy.LATEST  <br>   
SingleEmitter | 用来发射一条单一的数据，且一次订阅只能调用一次 <br>  singleEmitter.onSuccess()  <br>   <br>  // onSuccess与onError只可调用一个  ，否则会报异常 <br>  singleEmitter.onError()
Subject | 主题 , 不支持背压控制  <br>  一种特殊的存在 ，同时是 数据接收者 和 发送者
AsyncSubject|无论输入多少参数，永远只输出最后一个参数  ,  <br> 如果因为发生了错误而终止，AsyncSubject将不会发射任何数据
BehaviorSubject| 会发送离订阅最近的上一个值，没有上一个值的时候会发送默认值。  <br> 如果遇到错误会直接中断
PublishSubject|一旦一个观察者订阅了该Subject，它会发送所有数据给订阅者。  <br>  如果接收者只关系自己订阅的信息 ，可以用ofType来过滤  <br>  订阅者只会接受订阅之后的来自PublishSubject发射的数据。
ReplaySubject|无论何时订阅，都会将所有历史订阅内容全部发出
Processor|  rxjava2.x新增的 ，作用与Subject一样 。 <br> 支持背压控制
AsyncProcessor |
BehaviorProcessor |
PublishProcessor |
ReplayProcessor |
Transformer | 转换器 ，  实际上就是Func1<Observable , Observable>，  <br>  换句话说就是提供给他一个Observable它会返回给你另一个Observable
ObservableTransformer |
SingleTransformer |
CompletableTransformer |
FlowableTransformer |
MaybeTransformer |



## RxJava2.0中的观察者模式

RxJava2.0中的观察者模式| 说明
-|-
Observable / Observer | 不支持背压，及无法处理发送数据速度不协调的问题。  <br>  
Flowable/ Subscriber|
Single/ SingleObserver| 只发射一条单一的数据，或者一条异常通知，  <br>  不能发射完成通知，其中数据与通知只能发射一个。  <br>  // 可以指定Schedulers实现异步处理 ，如果不被订阅是不会被调用的  <br> Single.create  <br>   <br> // 接收传入的参数 ，是一种特殊的create() ，只会在当前线程里执行 , 不管是否被 订阅均会被调用  <br> Single.just  <br>   <br> // 仅仅用来连接Single顺序执行的，比如顺序执行检查网络，检查内存 ，注意：如果某个Single调用了onError()会导致被中断  <br> Single.concat  <br>   <br> // 将多个Single整合为一个  <br> Single.zip  <br>   <br> //   <br> Observable.just().zipWith()  <br>   <br> //创建一个自定义的操作符，用来处理数据发送者 。  <br> 就是对当前Observable进行操作，然后再返回它   <br> Single.compose  <br>   <br> //   <br> Single.subscribe  <br>   <br> // 返回一个错误， 一般用于调试  <br> Single.error  <br>   <br> // 用于一对0~多的返回  <br> Single.just( ... ).flatMap()  <br>   <br> // 一般map()是用于一对一的返回  <br> Single.just( ).map()  <br>   <br> // 它支持将Single转化为Observable对象，可以返回多个值  <br> Single.just( ).flatMapObservable()  <br>   <br> //  类似于concat ，如果有中断 ，后面都会中断  <br> Single.merge()  <br>   <br> //   <br> Single.just().mergeWith()  <br>   <br> // 用于指定异步任务的线程  <br> Single.just().subscribeOn()  <br>   <br> // 相当于try catch中的return，具体意思就是当函数抛出错误的时候给出一个返回值  <br> Single.just().onErrorReturn()  <br>   <br> // 指定回调所在线程  <br> Single.just().observeOn()  <br>   <br> // 超时设置  <br> Single.just().timeout()  <br>   <br> //   <br>  Single.just( ).toString()
Completable/ CompletableObserver|只发射一条完成通知，或者一条异常通知， <br> 不能发射数据，其中完成通知与异常通知只能发射一个
Maybe/ MaybeObserver|可发射一条单一的数据，以及发射一条完成通知，  <br>  或者一条异常通知，其中完成通知和异常通知只能发射一个，  <br>  发射数据只能在发射完成通知或者异常通知之前，否则发射数据无效。



##  RXjava2操作符
所谓的操作符就是一些被Rxjava封装好的方法或API ，使用这些操作符就可以完成线程调度，数据过滤等功能 。


操作符类型 | 说明
-|-
create()|创建被观察者对象
just()|快速的创建被观察者对象
fromArray()|将数组中的数据转换为Observable对象
fromIterable()|将List中的数据转换为Observable对象
empty()| 用于测试 ，仅发送Complete事件  
error()|用于测试 ，仅发送Error事件
never()|不发射数据，也永远不会结束
defer()|直到有观察者订阅时，才创建被观察者对象&发送事件  <br>  每次订阅后，都会得到一个刚创建的最新的Observable对象
timer()| 延迟指定时间后，发送1个数值0
interval()|  每隔指定时间就加n并发送出去
range()|连续发送一个事件序列，可指定范围
rangeLong()|跟 range()类似，支持数据类型为Long
intervalRange()|每隔指定时间 就发送 事件，可指定发送的数据的数量
map()  | 将传入数据处理后返回 。<br> 输入和输出是一对一
flatMap()| 输入和输出可以是 一对多 <br> 不能保证 输出的顺序和输入的顺序一致
concatMap| 输入和输出可以是一对对 <br>  输出和输入顺序 严格一致
buffer()|每次取n个事件放到缓存区中，n可配置
filter()| 将符合自定义条件的事件过滤出来
ofType()|过滤 特定数据类型的数据
skip()| 跳过正序的前 n 项
skipLast()| 跳过正序的后 n 项
distinct()| 去掉序列中重复的事件
distinctUntilChanged() | 去掉序列中连续重复的事件
take()| 指定观察者最多能接收到的事件数量
takeLast()| 指定观察者只能接收到被观察者发送的最后几个事件
throttleFirst()| 在某段时间内，只发送该段时间内第1次事件
throttleLast()| 在某段时间内，只发送该段时间内 最后1次事件
sample()| 指定时间内 取一次数据 ，其他的数据就会被丢掉
throttleWithTimeout() | 在输出了一个数据后的一段时间内，没有再次输出新的数据，  <br>   则把这个数据真正的发送出去；   <br>  假如在这段时间内有新的数据输出，则以这个数据作为将要发送的数据项，  <br>  并且重置这个时间段，重新计时
debounce () | 跟 throttleWithTimeout() 一样
firstElement() | 仅选取第1个元素
lastElement()  | 仅选取最后一个元素
elementAt()|  接收指定索引的某个元素 ， <br>  索引越界也不会报异常
elementAtOrError()| 接收指定索引的某个元素 ， <br>  索引越界 会报异常
all()| 判断发送的所有数据是否都满足自定义的条件 , true false
takeWhile()| 判断发送的每项数据是否满足 自定义条件   <br>  若满足条件则发送 ,不满足不发送
skipWhile()| 一直跳过，直到满足自定义条件
skipUntil()| -
takeUntil()| 一直发送 ，直到某个条件
sequenceEqual()| 两个数据源的数据是否相同
isEmpty()| 数据源的数据是否为空
contains()| 数据源中是否包含指定数据
defaultIfEmpty( obj)| 在不发送任何有效事件（ Next事件）、仅发送了 Complete 事件的前提下，发送个默认值obj
amb()| 当有多个数据源需要发送数据时 ， <br>  只发送 先发送数据的Observable的数据，而其余 Observable被丢弃。
concat()|组合多个被观察者一起发送数据，合并后 按发送顺序串行执行
concatArray()| 跟concat()类似
merge()|组合多个被观察者一起发送数据，合并后 按时间线并行执行  <br>  被观察者数量≤4
mergeArray()| 被观察者数量 > 4
mergeWith()|
delay()|使被观察者延迟一段时间再发送事件
concatDelayError()| 使用concat()，一旦某个被观察者发出onError事件，其他被观察者就会终止发送。 <br> 为了让其他被观察者继续发送，可以使用这个操作符
concatArrayDelayError()|
mergeDelayError()|
combineLatestDelayError()|
reduce()|把被观察者需要发送的事件聚合成1个事件、发送
collect()|将被观察者发送的数据事件收集到一个数据结构里
startWith()| 在被观察者发送事件前，追加发送一些数据
startWithArray()|
count()|统计被观察者发送事件的数量
subscribe()| 订阅， 连接观察者 和 被观察者
zip()|多个数据源Observable 发送数据，经过自定义组合处理后，观察者再收到 。 <br> <br> 1、组合数据时严格按照顺序从多个数据源中拿数据。<br> 2、最后观察者收到的数据个数 跟发送最少数据的数据源的数据个数相同。
x.zipWith()|
combineLatest()|如果子流1在等待其他流发射数据期间又发射了新数据，  <br> 则使用子流最新发射的数据进行合并
x.concatMap()|
x.scan()|
x.window()|
subscribeOn()| 指定发送事件的线程  <br>  只有第一次指定有效，其余的指定线程无效
unsubscribeOn()| 取消订阅
observeOn()|指定接收事件的线程  <br>  每次指定均有效
doOnEach() |每发送一次就会调用一次
doOnNext () |执行 next之前调用
doAfterNext()  |执行 next之后调用
doOnError() |
doOnComplete()|
doOnTerminate()|
doFinally()|
doOnSubscribe() |观察者订阅时调用
onErrorReturn()| 遇到错误时，发送1个特殊事件 & 正常终止
onErrorResumeNext()| 拦截的错误 是Throwable 类型
onExceptionResumeNext()|拦截的错误 是 Exception类型
retry()| 当捕捉到错误时，被观察者会重新发射数据
retryUntil()|遇到错误，重新发射，直到...
retryWhen()|
repeat()| 无条件地、重复发送事件
repeatWhen()|
repeatUntil()|
publish()|
share()|
connect()|


## 补充知识点
### 链式调用
```java
MsgInfo msgInfo = new MsgInfo();
// 链式调用
msgInfo.setOwnerId("100011002")
        .setStatus(MsgInfo.Status.SENDING)
        .setTime(System.currentTimeMillis());

// 普通调用
msgInfo.setOwnerId("100011002");
msgInfo .setStatus(MsgInfo.Status.SENDING) ;
msgInfo .setTime(System.currentTimeMillis());

// 链式调用的关键在于方法的返回值！
public MsgInfo setStatus(int status) {
    this.status = status;
    // 注意返回的是 this
    return this;
}
```
