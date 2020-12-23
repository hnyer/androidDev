# 安卓基本概念
## 双缓冲
```text
两个线程配合完成某一项工作，一个线程做A部分（例如绘制UI），
另一个线程做B事情（例如读取资源，计算数据）。 

常见的例子：
1、TCP数据传输， 数据的收发速度不一致可能导致数据的丢失。
数据线发送到缓冲区中，然后从缓冲区中取走。
有效地防止了数据的丢失。

2、复杂图像绘制过程中。 
线程A将一帧的计算结果放在缓冲区中，线程绘制完当前帧后再去缓冲区中取下一帧完整的绘制资源。 
这样就可以避免画面闪烁结果。

很多图形操作都很复杂，需要大量的计算，很难访问一次显示缓冲区就能写入待显示的完整图形数据，
通常需要多次访问显示缓冲区，每次访问时写入最新计算的图形数据。
而这样造成的后果是一个需要复杂计算的图形，你看到的效果可能是一部分一部分地显示出来的，造成很大的闪烁不连贯。
```



## 硬件加速
```text
图形的绘制如果是 GPU处理的就是 硬件加速绘制，如果是 CPU 处理的 就是软件绘制。
硬件加速使用 GPU 进行View上的绘制操作。

硬件加速可以在以下四个级别开启或关闭：
Application ：  为整个 APP开启
<application android:hardwareAccelerated="true" ...>

Activity：  activity 范围
<activity android:hardwareAccelerated="false" />

Window： window 范围
getWindow().setFlags( WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED

View：  view 范围
oneView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
```

并非所有的2D绘图操作支持硬件加速。所以硬件加速的开关分为四个层次。
以下是已知不支持硬件加速的绘图操作(需要最新的请查阅[官网](https://developer.android.google.cn/guide/topics/graphics/hardware-accel.html))


### 支持硬件加速情况

Canvas | 第一次支持 | Paint | 第一次支持
-|-
drawBitmapMesh() (colors array) | 18 |setAntiAlias() (for text)	|18
drawPicture()	 | 23 |setAntiAlias() (for lines)|	16
drawPosText() | 16 |setFilterBitmap()|	17
drawTextOnPath() | 16 |setLinearText()|	✗
drawVertices() | ✗ |setMaskFilter()|	✗
setDrawFilter() | 16 |setPathEffect() (for lines)	|✗
clipPath() | 18 |setRasterizer()	|✗
clipRegion() | 18 |setShadowLayer() (other than text)	|✗
clipRect(Region.Op.XOR) | 18 |setStrokeCap() (for lines)	|18
clipRect(Region.Op.Difference) | 18 |setStrokeCap() (for points)	|19
clipRect(Region.Op.ReverseDifference) | 18 |setSubpixelText()	|✗
clipRect() with rotation/perspective| 18 | |



Xfermode | 第一次支持 |Shader | 第一次支持
-|-
PorterDuff.Mode.DARKEN (framebuffer)	|✗|ComposeShader inside ComposeShader	|✗
PorterDuff.Mode.LIGHTEN (framebuffer)|	✗|Same type shaders inside ComposeShader	|✗
PorterDuff.Mode.OVERLAY (framebuffer)|	✗  | Local matrix on ComposeShader	|18


Drawing operation to be scaled	|第一次支持
-|-
drawText()	|18
drawPosText()	|✗
drawTextOnPath()	|✗
Simple Shapes*	|17
Complex Shapes*	|✗
drawPath()	|✗
Shadow layer	|✗
