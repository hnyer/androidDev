# BroadcastReceiver 广播
Service 向activity中传递数据，更新UI时不太方便，此时可以使用广播来实现。

```text
// 在 Service 中发送广播
Intent intent = new Intent();
intent.setAction( MyConfig.KEY_BROADCAST_ACTIONFLAG );
intent.putExtra(MyConfig.KEY_BROADCAST_MSG,msg);
service.sendBroadcast(intent);
```

```text
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
```


```text
// activity中注册广播
IntentFilter intentFilter = new IntentFilter();
intentFilter.addAction(MyConfig.KEY_BROADCAST_ACTIONFLAG);
broadcastReceiver = new BroadcastReceiver(msgCallBack);
registerReceiver(broadcastReceiver, intentFilter);
// 解绑广播
unregisterReceiver(broadcastReceiver);
```

```text
// 在回调接口中更新UI
@Override
public void onGetMsg(final String msg) {
    ((Activity)context).runOnUiThread(new Runnable() {
        @Override
        public void run() {
            showInfoTv.setText(msg);
        }
    });
}
```




