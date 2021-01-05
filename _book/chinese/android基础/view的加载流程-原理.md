#### Activity.java
Activity是通过Window间接的操纵View实例。
&nbsp;&nbsp;
&nbsp;
#### Window.java、PhoneWindow.java
Window是抽象类。PhoneWindow是Window的唯一实现类，
主要是为View提供展示策略，比如标题栏，默认背景颜色等。
&nbsp;&nbsp;
&nbsp;
####  DecorView.java
继承自FramentLayout 。
DecorView为整个Window界面的最顶层View。
用 Hierarchy Viewer这个工具查看DecorView的布局，发现继承自Activity和AppCompatActivity后的结构是不一样的。如果选用的主题不一样，结构也会不一样。具体结构请自行用工具查看分析。
但是我们自己添加的布局一般都是加载到 id/content的ViewGroup中去了。
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802020912_osChina_decorView结构.png)

&nbsp;&nbsp;
&nbsp;
#### ViewRootImpl.java
作为DecorView和WindowManager之间的桥梁。
&nbsp;&nbsp;
&nbsp;
#### WindowManager.java
管理Window。
&nbsp;&nbsp;
&nbsp;
#### WindowManagerService.java
android的窗口管理服务

&nbsp;&nbsp;
&nbsp;
#### Activity、Window、DecorView的关系
Activity中包含一个Window ,Window中包含一个DecorView .
```java
MainActivity.this.getWindow() .getDecorView();
```
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201802020947_osChina_activity_window_ecorview.png)


&nbsp;&nbsp;
&nbsp;
#### setContentView()后的加载流程

一路追踪源码，发现用户的view是被添加到mContentParent中。
```java
ViewGroup  mContentParent = generateLayout(mDecor);
ViewGroup contentParent = (ViewGroup)findViewById(ID_ANDROID_CONTENT);
public static final int ID_ANDROID_CONTENT = com.android.internal.R.id.content;
```
