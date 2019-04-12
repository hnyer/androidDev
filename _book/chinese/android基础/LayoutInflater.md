# LayoutInflater

它的作用类似于 findViewById(),不同点是LayoutInflater是用来找layout下xml布局文件，并且实例化！而findViewById()是找具体xml下的具体 widget控件。

```xml
LayoutInflater inflater1 = activity.getLayoutInflater();
LayoutInflater inflater2 = LayoutInflater.from(this);
LayoutInflater inflater3 = (LayoutInflater)activity.getSystemService(LAYOUT_INFLATER_SERVICE);

// XmlPullParser parser, ViewGroup root, boolean attachToRoot
// 参数意义：parser :R.layout.abc , root :root布局   attachToRoot :是否加到root布局中
View itemView=inflater1.inflate(R.layout.abc, null);

注意：
通过 inflate 获得的view 是获取到的长宽都是为0 。
// itemView.getWidth() 、itemView.getHeight() 都会为0

为了能获得长宽，我目前采用的是如下方式 ：
将这个布局放到启动页下 ，通过 findViewById 的方式获取到后 保存起来给其他用 。
```
