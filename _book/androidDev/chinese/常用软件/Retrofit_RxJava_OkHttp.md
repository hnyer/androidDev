# Retrofit_RxJava_OkHttp
```xml
Retrofit + RxJava + OkHttp

Retrofit 负责请求的数据和请求的结果
RxJava 负责异步，各种线程之间的切换
OkHttp 负责请求的过程
```


<br>
#### 添加依赖
```xml
// rxjava 核心库
compile 'io.reactivex.rxjava2:rxjava:2.1.14'
// Android 支持 Rxjava
compile 'io.reactivex.rxjava2:rxandroid:2.0.1'
// retrofit 核心库
compile 'com.squareup.retrofit2:retrofit:2.4.0'
// retrofit json 转换器
compile 'com.squareup.retrofit2:converter-gson:2.3.0'
//  衔接 Retrofit & RxJava
compile 'com.squareup.retrofit2:adapter-rxjava2:2.3.0'
// okhttp 核心库
compile 'com.squareup.okhttp3:okhttp:3.10.0'
// okhttp log 拦截器
compile 'com.squareup.okhttp3:logging-interceptor:3.8.1'
```


<br>
#### 定义 订阅者
```java
public class HttpSubscriber<T> implements Observer<T> {

    private PicInfoSubscriberListener subscriberOnListener;
	private Context context;
    private Disposable disposable;

    public HttpSubscriber( PicInfoSubscriberListener subscriberOnListener, Context context)
    {
        this.subscriberOnListener = subscriberOnListener;
		this.context = context;
    }

    @Override
    public void onSubscribe(@NonNull Disposable d) {
        disposable = d;
    }

    @Override
    public void onComplete() {
        if( (subscriberOnListener == null) ||  (context == null))
        {
            if(disposable != null && !disposable.isDisposed()){
                //切断订阅关系
                disposable.dispose();
            }
        }
    }

    @Override
    public void onError(Throwable e) {
        if(subscriberOnListener != null && context != null)
        {
            if (e instanceof SocketTimeoutException) {
                subscriberOnListener.onError(-1001, "网络超时，请检查您的网络状态");
            } else if (e instanceof ConnectException) {
                subscriberOnListener.onError(-1002, "网络链接中断，请检查您的网络状态");
            } else if(e instanceof MyException){
                subscriberOnListener.onError(((MyException)e).getCode(), ((MyException)e).getMsg());
            } else  {
                subscriberOnListener.onError(-1003, "未知错误:" + e.getMessage());
            }
        }  else  {
            if(disposable != null && !disposable.isDisposed()){
                disposable.dispose();
            }

        }
    }

    @SuppressWarnings("unchecked")
    @Override
    public void onNext(T t) {
        if(subscriberOnListener != null && context != null)
        {
            // 收到想要的订阅信息 ，然后通过回到接口 发送到需要数据的地方
            subscriberOnListener.onSucceed((List<PicBean>) t);
        }  else  {
            if(disposable != null && !disposable.isDisposed()){
                disposable.dispose();
            }
        }
    }
}
```

<br>
#### 定义 发布者 的能力接口
```java
/**
 *  最终的访问地址是 主地址 + 尾地址 http://xxx/getAllPicInfoGson?pageNum=6
 *  此处填写 尾地址
 */
@GET("getAllPicInfoGson")
Observable<List<PicBean>> getPicListByGet(@Query("pageNum") int pageNum );

@FormUrlEncoded
@POST("getAllPicInfoGson")
Observable<List<PicBean>> getPicListByPost(  @Field("pageNum") int pageNum );
```

<br>
#### 封装发布者
```java

/**
 *  封装 图片模块的http接口
 */
public class PicInfoApi {
    private final String TAG="PicInfoApi" ;
    private static PicInfoApi picInfoApi;
    private PicInfoService picInfoService;

    private PicInfoApi()
    {
        final String BASE_URL_PANDA = "https://www.aivin666.cn/AivinInfo/";
        picInfoService = HttpClient.getInstance(BASE_URL_PANDA).createApi(PicInfoService.class);
    }

    public static PicInfoApi getInstance()
    {
        if(picInfoApi == null)
        {
            picInfoApi = new PicInfoApi();
        }
        return picInfoApi;
    }



    public void getPicListByGet (Observer <List<PicBean> >subscriber , int pageNum)
    {
        // 通过操作符 map(...) 将接收的数据经过处理后再返回
        Observable observable = picInfoService.getPicListByGet(pageNum)  .flatMap( function );
        // 将观察者 和订阅者 关联起来
        toSubscribe(observable, subscriber);
    }

    public void getPicListByPost (Observer<List<PicBean>> subscriber , int pageNum)
    {
        // 通过操作符 map(...) 将接收的数据经过处理后再返回
        Observable   observable = picInfoService.getPicListByPost(pageNum) .flatMap( function );
        // 将观察者 和订阅者 关联起来
        toSubscribe(observable, subscriber);
    }

    private  void toSubscribe( Observable<List<PicBean>> o, Observer<List<PicBean>> s){
        // 运行在 io 线程中
       o.subscribeOn(Schedulers.io())
                //  取消订阅
                .unsubscribeOn(Schedulers.io())
                // 指定接收事件的线程
                .observeOn(AndroidSchedulers.mainThread())
                // 订阅
                .subscribe(s);
    }


    /***
     *  配合 操作符 map 使用
     *  Function<List<PicBean>, List<PicBean>>  第一个参数是输入数据类型 ， 第二个参数是输出类型 <br>
     *   List<PicBean> apply(List< PicBean> list)
     *   第一个   List<PicBean>  是返回数据类型 ， 第二个 List<PicBean>  是输入的数据类型
     *
     */
    private Function function = new Function<List<PicBean>,  Observable<List<PicBean> >     >() {
        @Override
        public Observable<List<PicBean> >   apply(List<PicBean> list) throws Exception {

            // 可以在这个函数里面进行数据过滤等处理
            if(list != null && list.size() > 0)
            {
                Log.i(TAG , " 数据处理="+ list.size()) ;
            }else{
                Log.i(TAG , " 数据处理  没有获取到数据") ;
            }
            return Observable.fromArray(list) ;
        }
    };
```


<br>
#### 进行http访问的相关配置
```java
public class HttpClient {
    private static String token = "";
    private static Retrofit retrofit;
    private static HashMap<String, HttpClient> clients = new HashMap<>();

    private HttpClient(String url) {
        retrofit = new Retrofit.Builder()
                // 设置接口主地址
                .baseUrl(url)
                // 配置 HTTP 请求客户端
                .client(getHttpRequestClient())
                // 配置json 转换器
                .addConverterFactory(GsonConverterFactory.create())
                // 添加支持 - 返回值定义为Observable对象
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .build();
    }


    public   <T> T createApi(Class<T> clazz) {
        // 获取接口实例
        return retrofit.create(clazz);
    }

    public static HttpClient getInstance(String url){
        if(!clients.containsKey(url))
        {
            HttpClient httpClient = new HttpClient(url);
            clients.put(url, httpClient);
        }
        // 一个主地址 对应一个client
        return clients.get(url);
    }


    /**
     *  定制 http 访问访问器
     */
    private OkHttpClient getHttpRequestClient() {
        HttpLoggingInterceptor logging = new HttpLoggingInterceptor( new OkHttpLogTool());
        // 打印获取到的信息。 （不设置不打印）
        logging.setLevel(HttpLoggingInterceptor.Level.BODY);
        //设置缓存路径
        String cacheDir = MyApplication.getInstance().getExternalCacheDir().getAbsolutePath() ;
        File httpCacheDirectory = new File(cacheDir , "OKHttpResponses");
        //设置缓存 大小
        Cache cache = new Cache(httpCacheDirectory, 50 * 1024 * 1024);
        // 自定义拦截器
        Interceptor interceptor = new Interceptor() {
            @Override
            public Response intercept(Chain chain) throws IOException {
                Request request = chain.request()
                        .newBuilder()
                        .addHeader("token", token)
                        .build();

                // 无网络时 ，使用缓存
                if (!NetUtil.isNetworkConnected(MyApplication.getInstance())) {
                    request = request.newBuilder()
                            .cacheControl(CacheControl.FORCE_CACHE)
                            .build();
                }

                Response response = chain.proceed(request);
                if (NetUtil.isNetworkConnected(MyApplication.getInstance())) {
                    // 有网络时 设置缓存超时时间0个小时
                    int maxAge = 0 * 60;
                    response.newBuilder()
                            .addHeader("Cache-Control", "public, max-age=" + maxAge)
                            // 清除头信息，因为服务器如果不支持，会返回一些干扰信息，不清除下面无法生效
                            .removeHeader("Pragma")
                            .build();
                } else {
                    // 无网络时，设置超时为1周
                    int maxStale = 60 * 60 * 24 * 7;
                    response.newBuilder()
                            .addHeader("Cache-Control", "public, only-if-cached, max-stale=" + maxStale)
                            .removeHeader("Pragma")
                            .build();
                }
                return response;
            }

        };

        OkHttpClient httpClient = new OkHttpClient.Builder()
                .addInterceptor(logging)
                .cache(cache)
                .addInterceptor(interceptor)
                .build();

        return httpClient;
    }
}
```

<br>
#### 如何使用
```java
private int pageNum = 6 ;
private void testUrlGet()
{
    PicInfoApi.getInstance().getPicListByGet(  new HttpSubscriber<List<PicBean>>(new PicInfoSubscriberListener () {
        @Override
        public void onSucceed(List<PicBean> data) {
          // 获取到数据
        }

        @Override
        public void onError(int code, String msg) {   }
    },  this) , pageNum);
}


private void testUrlPost( )
{
    PicInfoApi.getInstance().getPicListByPost(  new HttpSubscriber<List<PicBean>>(new PicInfoSubscriberListener () {
        @Override
        public void onSucceed(List<PicBean> data) {   }

        @Override
        public void onError(int code, String msg) {   }
    },  this) , pageNum);
}
```
