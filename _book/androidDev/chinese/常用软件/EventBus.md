# EventBus、
```xml
缺点：
不容易跟踪调试代码。逻辑不是很连贯。
```

<br>

```xml
https://github.com/greenrobot/EventBus
compile 'org.greenrobot:eventbus:3.1.1'

// 绑定
@Override
public void onStart() {
    super.onStart();
    EventBus.getDefault().register(this);
}

// 解绑
@Override
public void onStop() {
    super.onStop();
    EventBus.getDefault().unregister(this);
}

// 发送
EventBus.getDefault().post(new MessageEvent());

// 接收
@Subscribe(threadMode = ThreadMode.MAIN)
public void onEventBusMsgCome(WkEventBean bean) {
   switch (bean.getCode()){
       case WkEventBean.CODE_1:
           String msg = (String) bean.getData() ;
           WkLog.d("UI received =   "+msg);
           break;
       default:
           break;
   }

public class WkEventBean {
public static  final int CODE_1 = 1;
public static  final int CODE_2 = 2;
public WkEventBean(int code , Object data){
   this.code = code ;
   this.data =data ;
}

private int code ;
private Object data ;

public int getCode() {
   return code;
}
}       
```
