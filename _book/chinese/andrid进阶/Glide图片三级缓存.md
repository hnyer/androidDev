# Glide图片三级缓存

### [glide地址](https://github.com/bumptech/glide)



#### Glide 特点
1、Glide.with(xxx)方法 接受 Context、Activity 、 Fragment 。建议使用后面两个。因为图片加载会和Activity 、Fragment的生命周期保持一致。

2、支持加载 gif 格式。

---
&nbsp;&nbsp;

缓存类型 | 说明
-|-
原图|原始图片
处理图|经过压缩和变形等处理后的图片


&nbsp;&nbsp;

缓存类型 | 缓存策略 | 采用算法
-|-|-
1、内存缓存| 缓存处理图|LruCache + 弱引用
2、磁盘缓存|1、只缓存处理图 <br> 2、 只缓存原图 <br>  3、缓存原图和处理图  <br> 4、什么都不缓存|
3、网络资源||

&nbsp;&nbsp;

注： <br>  1、根据项目实际情况 ，可以设置 最大内存缓存空间大小、最大磁盘缓存空间大小、 缓存失效时间 。

#### LruCache 算法
（Least Recently Used Cache）近期最少使用 。核心思想是当缓存满时，会优先淘汰那些近期最少使用的缓存对象。

<br>
#### Glide 简单使用
```java
RequestOptions requestOptions = new RequestOptions()
         .placeholder(R.mipmap.icon_us)
         //.diskCacheStrategy(DiskCacheStrategy.NONE)
         //.skipMemoryCache(true)
         // 缓存失效策略 ，key 不一样 缓存就会失效
         .apply(RequestOptions.signatureOf(new ObjectKey(getSignatureKey())))
         .error(R.mipmap.icon_us);
 Glide.with(activity)
         // 根据路径、File 加载
         .load(filePath)
         .apply(requestOptions)
         .into(userImg);

private long   getSignatureKey()  {
   ///long key = System.currentTimeMillis() / INVALID_TIME
   long key = System.currentTimeMillis()  ;
   return key ;
}

// 当通过get访问接口 ，直接返回图片流的时候。 可以直接加载这个接口也可以显示。
// 不需要 去 加载接口返回的值。


```
