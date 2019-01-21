# [react-native](https://reactnative.cn/)
##  Windows下搭建React Native Android开发环境
```xml
//需要安装软件
1、python2.x  
2、node.js
3、react-native-cli
npm install -g yarn react-native-cli

4、webstorm
主流IED之一
5、android开发环境
jdk 、sdk之类的。为了方便可以通过通过Androidstudio来下载sdk。
开发过程用 webstorm即可。

//初始化项目
react-native init 你的项目名字
如果出现新建失败，可以尝试切换成淘宝源
npm config set registry https://registry.npm.taobao.org
npm config set disturl https://npm.taobao.org/dist

//安装新模块
npm install xxxxxx --save
安装成功后，库的依赖信息配置到package.json里面，具体内容被下载到node_module文件夹中。

//安装apk
在连接好设备的前提下
react-native run-android

//调试项目(debug模式才能)
1、adb shell input keyevent 82  打开调试窗口。
2、手机和电脑处于同一个局域网，确保能互相ping通。
3、电脑浏览器填写 http://localhost:8081/debugger-ui/（用ip会遇到跨域方面的错误）。
手机APP上要填写电脑的ip。（192.168.2.170:8081）
```

启动react native 服务，如果出现默认端口8081被占用的情况，可以指定端口。
react-native start --port 9999

测试是否启动成功
~~http://localhost:9999/index.android.bundle?platform=android~~

http://localhost:9999/



## 查看版本、修改版本
```xml
//查看本地版本
react-native --version
//查看最新版本
npm info react-native
//在package.json文件中修改成对应版本
"react-native": "^0.55.4"
//重新安装
npm install
```



## 项目结构说明
```xml
android        是一个可以用 Androidstudio 打开的android项目
ios            ios项目
index.js       打包 app 时进入 react native（js 部分） 的入口文件
App.js         可以理解为 react native（js 部分） 代码部分的入口文件
app.json       项目说明，主要给原生 app 打包用
package.json   项目依赖包配置文件
node_modules   依赖包安装目录
yarn.lock      yarn包管理文件
```


## 常用类和方法解释
常用类和方法|说明
-|-
ReactActivity |默认所有的Activity都继承它
ReactNativeHost |
ReactActivityDelegate|ReactActivity的逻辑代理实现
ReactRootView | React NativeUI的所在
ReactInstanceManager |  
XReactInstanceManagerImpl  |
ReactContext  | 管理React Native的状态等
NativeModule | 继承它的module可以在js端使用
Callback／Promise | 回调接口，与js端交互


### ReactNativeHost
Simple class that holds an instance of   ReactInstanceManager .
### ReactInstanceManager
??
### ReactPackage
可以看作是，向ReactNative注册了原生模块，这样在JS中你也可以使用原生模块的功能.
### NativeModule
A native module whose API can be provided to JS catalyst instances
### SoLoader
To load a native library
###  getJSMainModuleName()
Returns the name of the main module. Determines the URL used to fetch the JS bundle from the packager server .

## Props 、属性
大多数组件在创建时就可以使用各种参数来进行定制。用于定制的这些参数就称为props 。
props是内置的一个关键字
属性是不可以变的，一般用来传值

## 点击事件
```xml
 onPress={this._onPressButton}
 _onPressButton() {
    Alert.alert('You tapped the button!')
}
```


##  State 、状态
```xml
state是系统关键字
一切界面变化都是状态state变化引起的
state的修改必须通过setState()方法，其他方式无效
setState 只修改指定属性，并不影响其他没有指定的属性
setState 是异步操作，修改不会马上生效
```

## 样式
```xml
style是系统关键字
实际开发中组件的样式会越来越复杂，
我们建议使用StyleSheet.create来集中定义组件的样式。
```

## 弹性宽高 flex
```xml
理解参考android的权重比例。
flex 是系统关键字
一般使用flex:1来指定某个组件扩张以撑满所有剩余的空间。
并列的子组件的flex值越大，占用的空间比例就越大。
如果没有指定 flex 和 width、height ，容器尺寸就会为0 ，无法显示。
```


## 组件
### 文本输入框  TextInput
```xml
placeholder // 提示
onChangeText //监听内容变化
onSubmitEditing //监听提交
```

### 弹性布局  Flexbox
```xml
Flexbox规则可以在不同屏幕尺寸上提供一致的布局结构。

// flexDirection 主轴的方向
(row  、column)
flexDirection: 'column'  

//  justifyContent 子元素沿着主轴的排列方式
(flex-start 、center、flex-end、space-around、space-between  、 space-evenly)
justifyContent: 'space-evenly'  


//alignItems  决定其子元素沿着次轴的排列方式
(flex-start、center、flex-end 、 stretch)
alignItems: 'flex-end'
```

### 滚动视图 ScrollView
```xml
可以横向或纵向滚动
<ScrollView horizontal={false}>   </ScrollView>
```

### 普通长列表 FlatList
```xml
data //数据源
renderItem //每一行的视图
```


###  带分组的 长列表 SectionList
```xml
sections // 数据源
renderItem // 每一行的视图
renderSectionHeader //每一个组头的视图
keyExtractor //此函数用于为给定的item生成一个不重复的key。Key的作用是使React能够区分同类元素的不同个体
```


## 网络访问框架 Fetch
```xml
react native 内置支持 XMLHttpRequest（ajax）和 fetch框架 。推荐使用fetch。
react native 不支持jQuery 。因为jQuery包含了一些 react native 没有的东西。
```
