##### View
UI在主线程中绘制.

##### SurfaceView
继承自View，UI在一个子线程中进行绘制。

##### onMeasure()
测量View与Child View的大小

#### layout()
确定View自己的位置.

##### onLayout()
确定Child View的位置

#####  onSizeChanged()
View的大小不仅由View本身控制，而且受父控件的影响，所以我们在确定View大小的时候最好使用系统提供的onSizeChanged回调函数。

##### onDraw()
绘制View自己

#### dispatchDraw()
绘制 子View

##### onTouchEvent()
处理屏幕触摸事件
##### invalidate()
调用onDraw方法，重绘View中变化的部分

#### dispatchDraw



##### MeasureSpec
android使用MeasureSpec这类通过一个int数值计算“模式”和“大小”，前2位表示“大小的模式” ，后30bit表示 “尺寸大小”
```java
//MeasureSpec 源码
public static int getSize(int measureSpec)
{
    return (measureSpec & ~MODE_MASK);
}

public static int getMode(int measureSpec)
{
    return (measureSpec & MODE_MASK);
}


//父容器已经检测出子View所需要的精确大小。在该模式下，View的测量大小即为SpecSize。
MeasureSpec.EXACTLY ;
//父容器未能检测出子View所需要的精确大小，但是指定了一个可用大小即specSize
//在该模式下，View的测量大小不能超过SpecSize。
MeasureSpec.UNSPECIFIED;
//父容器不对子View的大小做限制.用得少。
MeasureSpec.AT_MOST;
```



##### dispatchTouchEvent()
当点击事件能够传递给当前View时，该方法就会被调用。
一个View接收到Touch，那么该Touch事件首先会传入到它的dispatchTouchEvent( )。

#### onInterceptTouchEvent()
事件拦截。当一个ViewGroup在接到MotionEvent事件序列时候，首先会调用此方法判断是否需要拦截。
ViewGroup特有的方法，View并没有。

##### onTouch()


##### performClick( )
这个方法会去调用onClick( )方法。所以在代码中调用performClick 来达到模拟人工点击的效果。


##### Configuration
获取 输入模式，屏幕大小， 屏幕方向等
##### ViewConfiguration
提供了一些标准常量，比如尺寸大小，滑动距离，敏感度等
##### GestureDetector
Android系统提供的手势监听类。可以简化许多操作。
##### VelocityTracker
用于跟踪触摸屏事件的速率。
##### Scroller
滑动、滚动工具类 。
##### ViewDragHelper
它提供了一系列用于处理用户拖拽子View的辅助方法和与其相关的状态记录。实现手指拖动其内部的某个View。
