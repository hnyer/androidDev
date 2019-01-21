# [Retrofit 2.x](https://github.com/square/retrofit)
```xml
一个支持 RxJava方式调用的网络请求框架
A type-safe HTTP client for Android and Java  by Square, Inc.
requires at minimum Java 7 or Android 2.3.

Retrofit 提供2种风格的网络请求方式：
1、传统风格 (略过不看)
采用Callback 接口  
2、RxJava 风格 (流行)
采用Observable接口
```

<br><br>

#### Retrofit2 + Rxjava2  依赖
```xml
// rxjava核心库
compile 'io.reactivex.rxjava2:rxjava:2.0.1'
// Android 支持 Rxjava
compile 'io.reactivex.rxjava2:rxandroid:2.0.1'
// Android 支持 Retrofit
compile 'com.squareup.retrofit2:retrofit:2.1.0'
// 衔接 Retrofit & RxJava
compile 'com.jakewharton.retrofit:retrofit2-rxjava2-adapter:1.0.0'
// 支持Gson解析
compile 'com.squareup.retrofit2:converter-gson:2.1.0'

```

<br><br>


注解|作用
-|-
@GET| get请求
@POST| post请求
@DELETE| delete请求
@HEAD| head请求
@OPTIONS| options请求
@PATCH| patch请求
@Headers|	添加请求头
@Path	| get请求 ，user/{password}  格式
@Query|	get请求 ， user/password?password=xxx 格式
@FormUrlEncoded	| 用表单数据提交
@Field|	 post需要  post请求需要 ， 替换参数

```java
//定义请求接口
public interface BlogService {
    /***
     * get 方式
     * https://www.aivin666.cn/AivinInfo/getAllMusicInfoGson?pageNum=2
     */
    @GET("getAllMusicInfoGson")
    Call<ResponseBody> getBlogByGet(@Query("pageNum") int pageNum);


    /***
     * post 方式
     * https://www.aivin666.cn/AivinInfo/getAllMusicInfoGson
     * @param pageNum  页码
     */
    @FormUrlEncoded
    @POST("getAllMusicInfoGson")
    Call<ResponseBody> getBlogByPost(@Field("pageNum") int pageNum);
}


//简单使用
Retrofit retrofit = new Retrofit.Builder()
        // url根目录
        .baseUrl("https://www.aivin666.cn/AivinInfo/")
        .build();

BlogService service = retrofit.create(BlogService.class);
//Call<ResponseBody> call = service.getBlogByGet(2);//get方式
Call<ResponseBody> call = service.getBlogByPost(2);//post方式
call.enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call,
     Response<ResponseBody> response) {
        try {
            String reslut = response.body().string();
            tvHttpResult.setText(reslut);
            Log.i(TAG , reslut) ;
        } catch (Exception e) { }
    }

    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {  }
});
```
