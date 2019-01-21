检验手机是否开启了View Server：
```html
adb shell service call window 3
//View Server处于关闭状态
返回：Result: Parcel(00000000 00000000 '........')"
//View Server处于开启状态
返回值：Result: Parcel(00000000 00000001 '........')"  
```

借助[ViewServer](https://github.com/romainguy/ViewServer)这个工具可以在任何手机上使用 hierarchyviewer。

注：在 android device monitor 可以找到 hierarchyviewer。  
