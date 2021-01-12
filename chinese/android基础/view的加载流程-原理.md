
# 相关类
#Activity.java
```text
Activity是通过Window间接的操纵View实例。
```

## Window.java、PhoneWindow.java
```text
Window是抽象类。
PhoneWindow是Window的唯一实现类，
主要是为 View 提供展示策略，比如标题栏，默认背景颜色等。
```

## WindowManager.java
```text
管理Window。
```
 
## WindowManagerService.java
```text
android的窗口管理服务
```
 
##  DecorView.java
```text
继承自FramentLayout 。
DecorView 为整个 Window 界面的最顶层 View。
用 Hierarchy Viewer这个工具查看DecorView的布局，
发现继承自 Activity 和 AppCompatActivity 后的结构是不一样的。
如果选用的主题不一样，结构也会不一样。具体结构请自行用工具查看分析。
但是我们自己添加的布局一般都是加载到 id/content 的 ViewGroup 中去了。
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802020912_osChina_decorView结构.png)

 
## ViewRootImpl.java 、ViewRoot(已经废弃)
```text
从字面上来看，ViewRootImpl 是 ViewRoot 的实现， 其实不是的。
ViewRoot 是Android2.2以前的，2.2之后就被 ViewRootImpl 替代了。
ViewRootImpl 作为 DecorView 和 WindowManager 之间的桥梁。
```

 
# Activity、Window、ViewRootImpl、DecorView之间的联系
```text
Activity中包含一个Window ,Window中包含一个DecorView 。
MainActivity.this.getWindow() .getDecorView();
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802020947_osChina_activity_window_ecorview.png)


 
#  setContentView()后的加载流程

一路追踪源码，发现用户的view是被添加到mContentParent中。
```text
ViewGroup  mContentParent = generateLayout(mDecor);
ViewGroup contentParent = (ViewGroup)findViewById(ID_ANDROID_CONTENT);
public static final int ID_ANDROID_CONTENT = com.android.internal.R.id.content;
```

#  PhoneWindow 是在哪里初始化的
```text

```
