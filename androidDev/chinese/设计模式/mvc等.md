# mvc
```xml  
1979年，Trygve Reenskaug 在Smalltalk-80系统上首次提出了MVC的概念，
最初的时候叫做Model-View-Controller-Editor。

MVC并不是一种设计模式，不在23种经典设计模式中。
可以理解为 MVC是一种架构模式，一种架构可能 运用到了多种设计模式。
MVC不是一种设计模式，而是多种设计模式的具体应用。
```


## Model 、 模型
适合做一些业务逻辑处理，比如数据库存取操作，网络操作，复杂的算法，耗时的任务等都在model层处理。

## View 、 视图
处理数据显示的部分，XML布局可以视为V层，显示Model层的数据结果。

## Controllor 、控制器
处理用户交互问题。

## mvc架构图
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801170903_osChina_p3.png)


## mvc优点
```xml
1、实现了分层开发
2、降低了代码的耦合
```

##  mvc不足
```xml
1、增加了代码编写的复杂性
2、解耦合不够彻底(MVC中允许Model和View进行交互)
```

<br><br>

# MVP
## mode  
业务逻辑和实体模型层

## view
视图展示层。 V层没有任何的逻辑部分又不用主动监听数据，被称之为“被动视图”。

## Presenter
负责view和model层的交互。

## mvp 特点
于mvc相比，mvp有以下特点
```xml
1、 mode层和view层只能通过 Presenter层进行交互
2、view层和Presenter层通过接口进行交互
3、一个复杂的view层可以对应多个presenter
```

## mvp架构图
![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801170903_osChina_p4.png)


## mvp优点
1、相对mvc解耦更彻底

## mvp缺点
1、多了很多接口文件。
在实际的开发过程中，很难严格遵循。（繁多的接口，繁琐的接口通信）



## mvp  demo(Android应用场景)

```java
/**
 * mode层中的接口
 */
public interface OnLoginListener{
    void loginSuccess(User user);
    void loginFailed();
}
```

```java
/**
 * mode层 ，实体对象
 */
public class User{
    private String username ;
    private String password ;
}
```


```java
/**
 * mode层，业务逻辑
 */
public class UserBiz{
    public void login(final String username, final String password,
        final OnLoginListener loginListener)  {
              //模拟子线程耗时操作
              new Thread()  {
                  @Override
                  public void run()  {
                      try  {
                          Thread.sleep(2000);
                      } catch (Exception e)  {  }

                      if ("fuckName".equals(username) && "fuckPwd".equals(password))  {
                          User user = new User();
                          user.setUsername(username);
                          user.setPassword(password);
                          loginListener.loginSuccess(user);
                      } else  {
                          loginListener.loginFailed();
                      }
                  }
              }.start();
        }//
}

```

```java
/**
 * view层和present层 通信的公共接口
 */
public interface IUserLoginView{
    String getUserName();
    String getPassword();
    void toMainActivity(User user);
    void showFailedError();
}
```


```java
/**
 * presenter层。
 */
public class UserLoginPresenter{
    private IUserLoginView userLoginView;
    private UserBiz userBiz;

    public UserLoginPresenter(IUserLoginView userLoginView){
        this.userLoginView = userLoginView;
        this.userBiz = new UserBiz();
    }

    /**
     * 对view开发的接口。
     * 与view进行交互
     */
    public void login()    {
        /**
         * present与mode层进行交互
         */
        userBiz.login(userLoginView.getUserName(), userLoginView.getPassword(), new OnLoginListener()
        {
            @Override
            public void loginSuccess(final User user)  {
                //与view进行交互(反馈信息)
                userLoginView.toMainActivity(user);
            }

            @Override
            public void loginFailed()  {
                //登录失败
                userLoginView.showFailedError();
            }
        });
    }
}
```


```java
/**
 *   View层。
 *   View通过接口与Presenter进行交互
 */
public class UserLoginActivity extends Activity implements View.OnClickListener ,IUserLoginView{
    private UserLoginPresenter mUserLoginPresenter = new UserLoginPresenter(this);

    @Override
    public void onClick(View v)  {
        mUserLoginPresenter.login();
    }//

    @Override
    public String getUserName()  {
        return "fuckName";
    }

    @Override
    public String getPassword()  {
        return  "fuckPwd";
    }

    public void toMainActivity(User user){
      //presenter 反馈登录成功
    }

    public void showFailedError()  {
      //presenter 反馈登录失败
    }

}
```

##  第三方MVP框架
1、[mosby](https://github.com/sockeqwe/mosby)

2、[MVPArms](https://github.com/JessYanCoding/MVPArms)


<br><br>

# MVVM
MVVM架构在一定程度上减少了MVP存在的以下缺点。
```xml
1、P层与V层是通过接口进行交互的，接口粒度不好控制。
粒度太小，就会存在大量接口的情况，使代码太过碎版化;
粒度太大，解耦效果不好。

2、V层与P层还是有一定的耦合度。
一旦V层某个UI元素更改，那么对应的接口就必须得改，
数据如何映射到UI上、事件监听接口这些都需要转变，牵一发而动全身。

3、复杂的业务同时也可能会导致P层太大，代码臃肿的问题依然不能解决，
这已经不是接口粒度把控的问题了，一旦业务逻辑越来越多，View定义的方法越来越多，
会造成Activity和Fragment实现的方法越来越多，依然臃肿。
```

## MVVM模块

模块 | 说明
-|-
M,model|业务逻辑和实体模型
V ,view|视图展示  
VM , viewModel| 完全是跟逻辑相关的代码

![](https://gitee.com/hnyer/filesOfGitbook/raw/master/files/201801170904_osChina_p6.png)

## mvvm的缺点
```xml
它将 View 的显示逻辑包含到了 View 中。不仅会造成混乱，
也让我们的测试和调试变的更加困难，因为它将逻辑和布局混淆在一起。
```

## [DataBinding](https://developer.android.google.cn/topic/libraries/data-binding/index.html)

我个人并不喜欢 DataBinding 这种方式 ，感觉挺麻烦，而且容易导致写出“面条代码”。

```xml
Data Binding Library  
谷歌官方出品的一款数据绑定工具 ， 是以MVVM思想指导实现的。
可以实现单向绑定或双向绑定，做到UI和数据的相互监听 。

DataBinding 使用了观察者模式实现的数据驱动。
```


### DataBinding 简单使用
```xml
1、开启android内置的 DataBinding
dataBinding {
    enabled true
}

2、.xml布局
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">
    <!--data部分是数据和UI的桥梁-->
    <data>

        <import type="gdcom.gov.databindingdemo.User"/>
        <variable
            name="user"
            type="User"/>
    </data>

    <!--ui布局部分-->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context="com.biaoqi.databindingstudy.MainActivity">

        <!--单向绑定-->
        <TextView
            android:text="@{user.name}"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <!--双向绑定-->
        <TextView
            android:text="@{user.age + ``}"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

    </LinearLayout>
</layout>

3、java代码赋值部分
//注意这个类的命名规则
ActivityMainBinding binding = DataBindingUtil.setContentView(this ,R.layout.activity_main) ;
User user = new User("tom" ,20);
binding.setUser(user);
```
