# Service启动方式
## 1. startService()

```
Intent intent = new Intent(OtherActivity.this,TestService.class);
startService(intent);
```

> 执行顺序：
>     onCreate() --> onStartCommand()  --> onStart()
## 2.Bind方式
### 1）简单控制 

```
Intent intent = new Intent(OtherActivity.this, TestService.class);
bindService(intent, serviceConnection, BIND_AUTO_CREATE);
```

### 2）通过AIDL通讯
> 要实现双向通讯，必须在一个aidl里面添加另一个aidl的引用，同时import不能省略。

> aidl文件必须建立在main文件下，与java同级的aidl文件下，然后使用as进行Rebuild project。

*代码如下：*

① ClientListener.aidl

```
package com.ikould.phonetest.service;
import com.ikould.phonetest.service.ServiceListener;

interface ClientListener {
    void start();
    void stop();
    void clear();
    void addServiceListener(ServiceListener serviceListener);
}
```
② ServiceListener.aidl

```
package com.ikould.phonetest.service;

interface ServiceListener{
    void onCreate();
    void onStart();
    void onDestroy();
    void onBind();
    void onUnBind();
}
```

> 客户端（Activity）

```
private void bindService() {
    Intent intent = new Intent(OtherActivity.this, TestService.class);
    bindService(intent, serviceConnection, BIND_AUTO_CREATE);
}

private ClientListener clientListener;

private ServiceConnection serviceConnection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        clientListener = ClientListener.Stub.asInterface(service);
        try {
            clientListener.addServiceListener(serviceListener);
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {

    }
};

private ServiceListener.Stub serviceListener = new ServiceListener.Stub() {
    @Override
    public void onCreate() throws RemoteException {
        Log.d("OtherActivity", "ServiceListener onCreate: ");
    }

    @Override
    public void onStart() throws RemoteException {
        Log.d("OtherActivity", "ServiceListener onStart: ");
    }

    @Override
    public void onDestroy() throws RemoteException {
        Log.d("OtherActivity", "ServiceListener onDestroy: ");
    }

    @Override
    public void onBind() throws RemoteException {
        Log.d("OtherActivity", "ServiceListener onBind: ");
    }

    @Override
    public void onUnBind() throws RemoteException {
        Log.d("OtherActivity", "ServiceListener onUnBind: ");
    }
};
```

> 服务端（Service）

```
@Nullable
@Override
public IBinder onBind(Intent intent) {
    if (mServiceListener != null)
        try {
            mServiceListener.onBind();
        } catch (RemoteException e) {
            e.printStackTrace();
        }
    return binder;
}

private ServiceListener mServiceListener;

private ClientListener.Stub binder = new ClientListener.Stub() {
    @Override
    public void start() throws RemoteException {
        Log.d("TestService", "start: ");
    }

    @Override
    public void stop() throws RemoteException {
        Log.d("TestService", "stop: ");
    }

    @Override
    public void clear() throws RemoteException {
        Log.d("TestService", "clear: ");
    }

    @Override
    public void addServiceListener(ServiceListener serviceListener) throws RemoteException {
        mServiceListener = serviceListener;
    }
};
```


