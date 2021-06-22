# BroadcastReceiver 全局广播
全局广播 发出去的消息可以被本机所有应用程序接收到 ，
Service 向activity中传递数据，更新UI时不太方便，此时可以使用广播来实现。

## 在 Service 向 Activity 传递信息
```text
// 在 Service 中发送广播
Intent intent = new Intent();
intent.setAction( MyConfig.KEY_BROADCAST_ACTIONFLAG );
intent.putExtra(MyConfig.KEY_BROADCAST_MSG,msg);
service.sendBroadcast(intent);
 
// 定义广播，并传入一个接口，方便回调给activity
public class BroadcastReceiver  extends  BroadcastReceiver {
    private MsgCallBack msgCallBack ;
    public BroadcastReceiver(MsgCallBack msgCallBack){
        this.msgCallBack =msgCallBack;
    }
    @Override
    public void onReceive(Context context, Intent intent) {
        String msg = intent.getStringExtra( MyConfig.KEY_BROADCAST_MSG);
        msgCallBack.onGetMsg(msg);
    }
}

// activity中注册广播
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(MyConfig.KEY_BROADCAST_ACTIONFLAG);
broadcastReceiver = new BroadcastReceiver(msgCallBack);
registerReceiver(broadcastReceiver, intentFilter);// 注册广播
unregisterReceiver(broadcastReceiver);// 解绑广播

// 在回调接口中更新UI
@Override
public void onGetMsg(final String msg) {
    ((Activity)context).runOnUiThread(new Runnable() {
        public void run() {
            showInfoTv.setText(msg);
        }
    });
}
```

# LocalBroadcastManager 本地广播
```text
本地广播 发出去的消息只能被自己所在的应用程序接收到。
因为消息不会广播范围不会超过自身程序，所以数据安全性和广播效率都比全局广播高。
本地广播是无法通过静态注册的方式来接收的。

LocalBroadcastManager localBroadcastManager = LocalBroadcastManager.getInstance(this);
Intent intent = new Intent(MyConfig.KEY_INTENT_FLAG );
intent.putExtra(MyConfig.KEY_BROADCAST_MSG, "hello msg");
localBroadcastManager.sendBroadcast(intent);//发送广播

MyLocalBroadcastReceiver localReceiver = new MyLocalBroadcastReceiver();
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(MyConfig.KEY_INTENT_FLAG ); //匹配指定的 Intent
localBroadcastManager.registerReceiver(localReceiver, intentFilter);//注册监听
unregisterReceiver(localReceiver);//取消监听
```

## 为什么本地广播的效率比全局广播高
BroadcastReceiver 的通信是走 Binder 机制 。
而 本地广播 走的是 Handler 机制 ，只是利用到了 IntentFilter 的 match 功能。
不用消耗跨进程的资源消耗，自然就效率高。
```text
// LocalBroadcastManager.java 
private LocalBroadcastManager(Context context) {
    mHandler = new Handler(context.getMainLooper()) {
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case MSG_EXEC_PENDING_BROADCASTS:
                    executePendingBroadcasts();// 处理 
                    break;
                default:
                    super.handleMessage(msg);
      }   }   }; }
```

