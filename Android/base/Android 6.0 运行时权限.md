# Normal Permissions如下
1. ACCESS_LOCATION_EXTRA_COMMANDS
1. ACCESS_NETWORK_STATE
1. ACCESS_NOTIFICATION_POLICY
1. ACCESS_WIFI_STATE
1. BLUETOOTH
1. BLUETOOTH_ADMIN
1. BROADCAST_STICKY
1. CHANGE_NETWORK_STATE
1. CHANGE_WIFI_MULTICAST_STATE
1. CHANGE_WIFI_STATE
1. DISABLE_KEYGUARD
1. EXPAND_STATUS_BAR
1. GET_PACKAGE_SIZE
1. INSTALL_SHORTCUT
1. INTERNET
1. KILL_BACKGROUND_PROCESSES
1. MODIFY_AUDIO_SETTINGS
1. NFC
1. READ_SYNC_SETTINGS
1. READ_SYNC_STATS
1. RECEIVE_BOOT_COMPLETED
1. REORDER_TASKS
1. REQUEST_INSTALL_PACKAGES
1. SET_ALARM
1. SET_TIME_ZONE
1. SET_WALLPAPER
1. SET_WALLPAPER_HINTS
1. TRANSMIT_IR
1. UNINSTALL_SHORTCUT
1. USE_FINGERPRINT
1. VIBRATE
1. WAKE_LOCK
1. WRITE_SYNC_SETTINGS

# Dangerous Permissions(不同的手机有些不同，可以通过adb shell pm list permissions -d -g进行查看)
1. group:android.permission-group.CONTACTS
permission:android.permission.WRITE_CONTACTS
permission:android.permission.GET_ACCOUNTS
permission:android.permission.READ_CONTACTS

1. group:android.permission-group.PHONE
permission:android.permission.READ_CALL_LOG
permission:android.permission.READ_PHONE_STATE
permission:android.permission.CALL_PHONE
permission:android.permission.WRITE_CALL_LOG
permission:android.permission.USE_SIP
permission:android.permission.PROCESS_OUTGOING_CALLS
permission:com.android.voicemail.permission.ADD_VOICEMAIL

1. group:android.permission-group.CALENDAR
permission:android.permission.READ_CALENDAR
permission:android.permission.WRITE_CALENDAR

1. group:android.permission-group.CAMERA
permission:android.permission.CAMERA

1. group:android.permission-group.SENSORS
permission:android.permission.BODY_SENSORS

1. group:android.permission-group.LOCATION
permission:android.permission.ACCESS_FINE_LOCATION
permission:android.permission.ACCESS_COARSE_LOCATION

1. group:android.permission-group.STORAGE
permission:android.permission.READ_EXTERNAL_STORAGE
permission:android.permission.WRITE_EXTERNAL_STORAGE

1. group:android.permission-group.MICROPHONE
permission:android.permission.RECORD_AUDIO

1. group:android.permission-group.SMS
permission:android.permission.READ_SMS
permission:android.permission.RECEIVE_WAP_PUSH
permission:android.permission.RECEIVE_MMS
permission:android.permission.RECEIVE_SMS
permission:android.permission.SEND_SMS
permission:android.permission.READ_CELL_BROADCASTS

# 申请权限步骤
1. 在AndroidManifest文件中添加需要的权限。
1. 检查权限
```
if (ContextCompat.checkSelfPermission(thisActivity,Manifest.permission.READ_CONTACTS) != PackageManager.PERMISSION_GRANTED) {
}else{
    //
}
```
> 这里涉及到一个API，ContextCompat.checkSelfPermission，主要用于检测某个权限是否已经被授予，方法返回值为PackageManager.PERMISSION_DENIED或者PackageManager.PERMISSION_GRANTED。当返回DENIED就需要进行申请授权了。
1. 申请授权

```
ActivityCompat.requestPermissions(thisActivity,
new String[]{Manifest.permission.READ_CONTACTS},
MY_PERMISSIONS_REQUEST_READ_CONTACTS);
```
> 该方法是异步的，第一个参数是Context；第二个参数是需要申请的权限的字符串数组；第三个参数为requestCode，主要用于回调的时候检测。可以从方法名requestPermissions以及第二个参数看出，是支持一次性申请多个权限的，系统会通过对话框逐一询问用户是否授权。
1. 处理权限申请回调

```
@Override
public void onRequestPermissionsResult(int requestCode,
    String permissions[], int[] grantResults) {
    switch (requestCode) {
        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
            // If request is cancelled, the result arrays are empty.
            if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                // permission was granted, yay! Do the
                // contacts-related task you need to do.
            } else {
                // permission denied, boo! Disable the
                // functionality that depends on this permission.
            }
            return;
        }
    }
}
```
> ok，对于权限的申请结果，首先验证requestCode定位到你的申请，然后验证grantResults对应于申请的结果，这里的数组对应于申请时的第二个权限字符串数组。如果你同时申请两个权限，那么grantResults的length就为2，分别记录你两个权限的申请结果。如果申请成功，就可以做你的事情了~

1. 还有个API值得提一下：

```
// Should we show an explanation?
if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
    Manifest.permission.READ_CONTACTS)) 
    // Show an expanation to the user *asynchronously* -- don't block
    // this thread waiting for the user's response! After the user
    // sees the explanation, try again to request the permission.
}
```
> 这个API主要用于给用户一个申请权限的解释，该方法只有在用户在上一次已经拒绝过你的这个权限申请。也就是说，用户已经拒绝一次了，你又弹个授权框，你需要给用户一个解释，为什么要授权，则使用该方法。

1. 上述几个步骤结合到一起就是：

```
// Here, thisActivity is the current activity
if (ContextCompat.checkSelfPermission(thisActivity,
        Manifest.permission.READ_CONTACTS)
    != PackageManager.PERMISSION_GRANTED) {
    // Should we show an explanation?
    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
        Manifest.permission.READ_CONTACTS)) {
        // Show an expanation to the user *asynchronously* -- don't block
        // this thread waiting for the user's response! After the user
        // sees the explanation, try again to request the permission.
    } else {
        // No explanation needed, we can request the permission.
        ActivityCompat.requestPermissions(thisActivity,
            new String[]{Manifest.permission.READ_CONTACTS},
            MY_PERMISSIONS_REQUEST_READ_CONTACTS);
        // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an
        // app-defined int constant. The callback method gets the
        // result of the request.
    }
}
```


