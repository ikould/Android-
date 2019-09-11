# 两种调用方式
## 一、使用so库

1. 在app下创建jni包，再创建一个.c/.cpp文件
    
```
#include <jni.h>

jstring
// Java_包名_方法名
Java_com_ikould_testnative_NdkJniUtils_getTestNdkJni(JNIEnv *env, jobject thiz) {
    return (*env)->NewStringUTF(env, "I`m Str !");
}
```

2. jni下创建一个Android.mk

```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := demo
LOCAL_SRC_FILES := demo.c
include $(BUILD_SHARED_LIBRARY)
```
3. 在Terminal中执行
-     1) 进入到jni所在目录
-     2）执行ndk-build，libs下生成so库，app下生成object（可删除）
4. app grade中添加：

```
sourceSets {
    main {
        jniLibs.srcDirs = ['libs']
    }
}
```
5. 调用Native方法的类添加 System.loadLibrary("demo");如下：

```
public class NdkJniUtils {

    public native String getTestNdkJni();

    static {
        System.loadLibrary("demo");
    }
}
```
## 二、直接使用c++调用

> 说明：可根据Android Studio创建一个c++的Android项目

#### 注意点：
1. 调用Native方法中一点要把C++引入：

```
public native String stringFromJNI();

static {
    System.loadLibrary("native-lib");
}
```
2. app下build.gradle

```
externalNativeBuild {
    cmake {
        path "CMakeLists.txt"
    }
}
```
3. app下有CMakeLists.txt文件，格式（待定）
