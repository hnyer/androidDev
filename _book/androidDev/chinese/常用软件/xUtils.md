# xUtils
```xml
https://github.com/wyouflf/xUtils3

compile 'org.xutils:xutils:3.5.0'
```

```java
// 1、在Application 初始化
// import org.xutils.x;
x.Ext.init(this);
x.Ext.setDebug(false);

// get请求

private void loadData( ) {
    String API = Ipconfig.KEY_WORKNEWS_DetailItem;
    API = API.replace("{pageSize}" ,"10") ;
    RequestParams params = new RequestParams(API);
    Callback.Cancelable cancelable = x.http().get(params,
            new Callback.CommonCallback<String>() {
                @Override
                public void onSuccess(String result) { }

                @Override
                public void onError(Throwable ex, boolean isOnCallback) {   }

                @Override
                public void onCancelled(CancelledException cex) {   }

                @Override
                public void onFinished() {   }
            });
}

// post 请求
private void requestHost(String name, String pwd1 ) {
    String API = Ipconfig.KEY_userRegister;
    RequestParams params = new RequestParams(API);
    params.addBodyParameter("userName",name);
    params.addParameter("password",pwd1);
    x.http().post(params, new Callback.CommonCallback<String>() {
        @Override
        public void onSuccess(String result) {   }

        @Override
        public void onError(Throwable ex, boolean isOnCallback) {   }

        @Override
        public void onCancelled(CancelledException cex) { }

        @Override
        public void onFinished() {   }
    });


    // 文件上传
String api ="http://xxx" ;
RequestParams params = new RequestParams(api) ;
params.setMultipart(true);
params.addBodyParameter("userId", MyApplication.getUserId(activity));
params.addBodyParameter("file", new File( filePath));
Callback.Cancelable cancelable
        = x.http().post(params,
        new Callback.CommonCallback<String>() {
            @Override
            public void onSuccess(String result) {   }

            @Override
            public void onError(Throwable ex, boolean isOnCallback) { }

            @Override
            public void onCancelled(CancelledException cex) {     }

            @Override
            public void onFinished() { }
        });
```
