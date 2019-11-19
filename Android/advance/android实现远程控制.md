# 前言
> 最近在搞一个Android端，类似TeamViewer的东西。
目前已经实现：在微信中打开一个网址，远程控制Android端页面操作（点击、滑动、回到首页、打开指定APP（比如说钉钉）、熄屏和亮屏）。

# 设备要求
一台Android手机（Android7.0以上，模拟点击需要api>=24）、任意可以打开网页的设备（我用的是iphone的微信打开的网页）和JavaWeb服务器

# 原理
> 网页发送指令给服务器，同时实时拉取Android设备上传的截屏；Android设备实时获取指令，根据指令确定 是否实时上传图片、滑动的笔迹、熄屏、亮屏、打开指定APP和回到首页等操作；Java服务器充当中转站。主要通过图片进行交互，图片有个缺点是h5加载太慢，之前也考虑过使用RTMP实时传输视频流，Android端倒是有现成的代码，但苦于对JS的拉流端不太了解。So，就使用图片将就下了。

# 难点
> 要实现以上操作，主要难点如下：
1. 对于不太擅长后台Java代码的我来说，部署服务器、上传下载图片等也算是一个难点。但这不是本篇讨论的重点，多google下也就解决了。
2. 其次是JS代码编写，网络请求、触摸事件采集等。

**本篇重点是Android端的难点**
1. 截屏
2. 模拟滑动点击

# 截屏
> 网页端要想发送指令，前提是收到可见的页面，如此才能发送正确的笔迹，页面资源来源于Android端的实时上传。所以截屏至关重要。
之前一直以为截屏只有Root下通过shell命令或者连接usb通过adb执行shell命令才能截屏，但后来查阅了相关文章后发现，Android可以通过MEDIA_PROJECTION_SERVICE来实时截屏。步骤如下：

## 打开申请录屏的弹窗
```
mMediaProjectionManager = (MediaProjectionManager) getApplication().getSystemService(Context.MEDIA_PROJECTION_SERVICE);
startActivityForResult(mMediaProjectionManager.createScreenCaptureIntent(), REQUEST_MEDIA_PROJECTION);
```
## 创建ImageReader，用于接收屏幕数据
```
mImageReader = ImageReader.newInstance(windowWidth, windowHeight, 0x1, 2); //ImageFormat.RGB_565
mMediaProjection = mMediaProjectionManager1.getMediaProjection(mResultCode, mResultData);
mVirtualDisplay = mMediaProjection.createVirtualDisplay("screen-mirror",
windowWidth, windowHeight, mScreenDensity, DisplayManager.VIRTUAL_DISPLAY_FLAG_AUTO_MIRROR,
mImageReader.getSurface(), null, null);
```
## 通过ImageReader截取当前手机屏幕
```
Image image = mImageReader.acquireLatestImage();
if(image == null){ // 最后一帧如果被获取过，间隔短的话，后一帧可能还没生成。
return;
}
int width = image.getWidth();
int height = image.getHeight();
final Image.Plane[] planes = image.getPlanes();
final ByteBuffer buffer = planes[0].getBuffer();
int pixelStride = planes[0].getPixelStride();
int rowStride = planes[0].getRowStride();
int rowPadding = rowStride - pixelStride * width;
Bitmap bitmap = Bitmap.createBitmap(width + rowPadding / pixelStride, height, Bitmap.Config.ARGB_8888);
bitmap.copyPixelsFromBuffer(buffer);
bitmap = Bitmap.createBitmap(bitmap, 0, 0, width, height);
image.close();
```
## 保存Bitmap，上传到服务器

## 关闭预览
```
mVirtualDisplay.release();
mVirtualDisplay = null;
mMediaProjection.stop();
```
以上，截屏就已经讲完了，下面介绍下模拟点击
# 模拟滑动点击
> 最初的思路来源于抢红包app(代码：https://github.com/lendylongli/qianghongbao)，其本质是通过AccessibilityService，获取到手机的特殊权限，以此来获取app页面元素，操控页面按钮，模拟点击。开始是在钉钉上进行获取测试，一步步分析页面对应的ID，通过AccessibilityService进入到考勤界面。但钉钉的webview那个Activity貌似对AccessibilityService进行了限制，无法在考勤界面获取到页面元素（并非WebView的原因，工作界面依然有WebView，但却可以找到并进入考勤界面）。所以到此为止，我们无法通过抢红包的思路实现准确的一键打开。当然，已经进入到考勤页面了，上班和下班打开的按钮基本是固定的，所以简单点记录下页面坐标也就可以实现。但这是不完美的，万一有什么误差没打卡成功呢，我就要补卡了啊。。
所以我采用了另一种方案：直接远程控制，这样就不针对钉钉了，而是手机端的所有操作。以后可以扩展的地方也更多。

**模拟滑动操作的api依然是AccessibilityService，不过现在换成了dispatchGesture方法，该方法要求API >= Android N**
核心代码如下：
```
@TargetApi(Build.VERSION_CODES.N)
AccessibilityService.GestureResultCallback callback2 = new AccessibilityService.GestureResultCallback() {
@Override
public void onCompleted(GestureDescription gestureDescription) {
super.onCompleted(gestureDescription);
Log.d(TAG, "gesture completed");
}

@Override
public void onCancelled(GestureDescription gestureDescription) {
super.onCancelled(gestureDescription);
Log.d(TAG, "gesture cancelled");
}
};

@TargetApi(Build.VERSION_CODES.N)
private static GestureDescription createGestureDescription(Path path, int duration) {
GestureDescription.StrokeDescription clickStroke = new GestureDescription.StrokeDescription(path, 0, duration);
GestureDescription.Builder clickBuilder = new GestureDescription.Builder();
clickBuilder.addStroke(clickStroke);
return clickBuilder.build();
}

boolean result = dispatchGesture(createGestureDescription(path, duration), callback, null);
```
我们可以通过path让AccessibilityService对屏幕进行模拟触摸，从而实现对手机的控制。
目前有个不足之处在于，传入的path，不管有多少个点，最终只取开始和结尾两个点，down取第一个点、move和up取最后一个点。如果path只有一个点，那么没有move，up和down为同一个点。这里也需要我继续研究..


