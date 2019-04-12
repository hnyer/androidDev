# Fragment详解
一个Fragment总是嵌入到一个Activity，是一个模块化的View，它的生命周期受它所嵌入的Activity生命周期的的影响。可以动态的添加、替换、移除某个Fragment。

## fragment的优势
同样的界面 ， fragment 比activity 占用内存更少 ，响应速度更快 。


## Fragment生命周期
### activity与fragment生命周期的关系
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201803011033_osChina_fragment生命周期关系.png)

### 与activity相同的生命周期函数
onCreate 、onStart 、onResume、onPause、onStop 、onDestroy

### 与activity相比多出的生命周期函数
onAttach 、onDetach 、onActivityCreated 、onViewCreated 、onCreateView 、onDestroyView





## 常用函数
### getSupportFragmentManager
获得碎片管理者

### getChildFragmentManager
获取自己的FragmentManager对象。

### getFragmentManager
获取的是父Fragment(如果没有，则是FragmentActivity)的FragmentManager对象。

### getBackStackEntryCount
从管理器中得到Fragment当前已加入Fragment回退栈中的fragment的数量。
