# svg绘制不规则图形
```xml
思路一： 参考 https://github.com/zhangjundi/taiwanDemo
1、加载、解析 svg文件（一般为.xml） ，获得 path 数据
2、将path数据转换为 Path 对象。
谷歌官方有现成的工具类 PathParser.java。

3、将解析出来的所有path绘制出来
4、判断点击点是否在当前path范围内。

public boolean isInArea(Path mPath ,float x, float y){
    RectF r=new RectF();
    mPath.computeBounds(r, true);
    Region re=new Region();
    re.setPath(mPath, new Region((int)r.left,(int)r.top,(int)r.right,(int)r.bottom));
    return  re.contains((int)x, (int)y);
}
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201809111151_osChina_p7.png)


<br>
##### vector
```xml
Android 5.0发布的时候，Vector只支持Android 5.0+。
不过自从AppCompat 23.2之后，Google做了兼容处理 ，
只需要引用com.android.support:appcompat-v7:23.2.0以上的版本就 适用于Android 2.1以上的所有系统 。


//SVG（Scalable Vector Graphics ）矢量图
这种图像格式在前端中已经使用的非常广泛了

//Vector  (Vector Drawable)
就是Android中的矢量图，可以说Vector就是Android中的SVG实现，
因为Android中的Vector并不是支持全部的SVG语法（也没有必要）

```

<br>

vector标签属性 |意义
-|-
width|图形宽度
height|图形高度
viewportHeight| 画布高度
viewportWidth|画布宽度
group |对Path进行分组
path| 对应一个Path<br>name // path名字<br>fillAlpha//填充透明度<br>fillColor //填充颜色<br>strokeColor//线条颜色 <br>pathData //path指令<br>strokeWidth//线条宽度  

<br>

[Android基于vector文件绘制不规则图形的例子](https://github.com/hnyer/RegionDetector)
![](https://raw.githubusercontent.com/hnyer/RegionDetector/master/p8.png)
