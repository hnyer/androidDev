# 屏幕坐标系
```text
在Android系统中，屏幕的左上角是坐标系统的原点（0,0）坐标。
原点向右延伸是X轴正方向，原点向下延伸是Y轴正方向。
```

![](../pics/201801301450_osChina_坐标系.png)


# View坐标系
```text
View坐标系以父视图的左上角为坐标原点。
getTop();   //获取子View左上角距父View顶部的距离
getLeft();  //获取子View左上角距父View左侧的距离
getBottom();//获取子View右下角距父View顶部的距离
getRight(); //获取子View右下角距父View左侧的距离
view.setX() // x = left + translationX, 每一次 setTranslationX 都会导致getX值的变化，而 left 的值并不会改变。
view.getX() //
view.getY() // 
view.setTranslationX() // 相对于left属性来进行的平移，而不是它上一次的translationX后的位置 ,比如 setTranslationX(30)后，就是相对于left 往右平移30px.

event.getX(); //触摸点相对于其所在视图坐标的坐标    
event.getY();
event.getRawX();//触摸点相对于屏幕的坐标
event.getRawY();
```

![](../pics/坐标体系.png)
 


