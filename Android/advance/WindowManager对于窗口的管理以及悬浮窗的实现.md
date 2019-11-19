# 前言
> 本篇摘自https://blog.csdn.net/qq_33275597/article/details/78429818。对于Window相关总结的非常好，在这里收藏下。

# WindowManager
WindowManager是窗口管理器, 所有显示窗口都可以通过它来控制

```
// 添加一个View到窗口中
WindowManager.addView(view, layoutParams)
// 更新窗口中的View属性
WindowManager.updateView(view, layoutParams)
// 删除窗口中的View
WindowManager.removeView(view)
```

# WindowManager.LayoutParams
```
// 获取布局参数
WindowManager.LayoutParams params = getWindow().getAttributes();
// 创建布局参数
WindowManager.LayoutParams params = new WindowManager.LayoutParams();
```
# WindowManager.LayoutParams.type
用于确定窗口在屏幕上的显示层次

```
// 普通应用的第一个窗口
FIRST_APPLICATION_WINDOW
// 作为所有应用基础的窗口, 其他应用窗口都在其上
TYPE_BASE_APPLICATION
// 普通应用窗口. token必须为Activity的token, 指明该窗口属于谁
TYPE_APPLICATION
// 应用启动时显示的窗口. 用于系统在应用能够显示之前显示一些东西
TYPE_APPLICATION_STARTING
// 应用最后一种窗口类型
LAST_APPLICATION_WINDOW
// 子窗口
FIRST_SUB_WINDOW
// 在应用窗口之上的面板窗口, 出现在所依附的窗口之上
TYPE_APPLICATION_PANEL
// 显示媒体(如视频)的窗口, 在他们依附的窗口之下显示
TYPE_APPLICATION_MEDIA
// 应用窗口之上的子面板窗口, 显示在所依附的窗口和其他面板之上
TYPE_APPLICATION_SUB_PANEL
// 类似TYPE_APPLICATION_PANEL, 但会作为顶层窗口, 而不是容器的子窗口
TYPE_APPLICATION_ATTACHED_DIALOG
// @hide 在媒体窗口上显示覆盖层的窗口 
// 显示在TYPE_APPLICATION_MEDIA和应用窗口之间
TYPE_APPLICATION_MEDIA_OVERLAY
// 一个子面板窗口, 在应用窗口和子面板窗口之上
TYPE_APPLICATION_ABOVE_SUB_PANEL
// 最后一个子窗口
LAST_SUB_WINDOW

// 第一个系统窗口
FIRST_SYSTEM_WINDOW
// 状态栏
// 只能有一个状态栏窗口. 放置在屏幕上方, 其他所有窗口都在其之下
TYPE_STATUS_BAR
// 搜索条
// 只能有一个搜索条窗口, 放置在屏幕顶层
TYPE_SEARCH_BAR
// 电话窗口
// 这是非应用窗口, 用于来电的界面 该窗口通常置于所有应用之上, 但在状态栏下
TYPE_PHONE
// 系统窗口, 例如低电量警告弹窗, 在应用窗口之上
TYPE_SYSTEM_ALERT
// 锁屏窗口
TYPE_KEYGUARD
// 透明通知. 不会拦截触摸事件, 可以向下透传
TYPE_TOAST
// 系统覆盖窗口, 在所有东西之上. 该窗口必须禁止获取输入焦点, 否则会变成锁屏
TYPE_SYSTEM_OVERLAY
// 优先级电话, 即使锁屏也会显示
// 该窗口必须禁止获取输入焦点, 否则会变成锁屏
TYPE_PRIORITY_PHONE
// 状态栏拉出的面板
TYPE_SYSTEM_DIALOG
// 锁屏
TYPE_KEYGUARD_DIALOG
// 系统错误窗口, 在所有内容之上
TYPE_SYSTEM_ERROR
// 输入法窗口, 在普通UI之上
// 可以缩放
TYPE_INPUT_METHOD
// 输入法对话框窗口, 在当前输入法窗口之上
TYPE_INPUT_METHOD_DIALOG
// 壁纸窗口, 在任意窗口之下, 壁纸之上
TYPE_WALLPAPER
// 状态栏拉出的面板
TYPE_STATUS_BAR_PANEL
// 安全的系统覆盖窗口, 在所有内容之上
// 必须禁止获取输入焦点, 否则会变成锁屏
// 同TYPE_SYSTEM_OVERLAY类似, 区别是只允许系统创建这种覆盖层, 应用无法创建
TYPE_SECURE_SYSTEM_OVERLAY
// 拖拽窗口 最多有一个, 在所有窗口之上
TYPE_DRAG
// 状态栏拉出的面板, 在状态栏之下
TYPE_STATUS_BAR_SUB_PANEL
// 鼠标指针
TYPE_POINTER
// 导航条
TYPE_NAVIGATION_BAR
// 调整音量时显示的音量窗口
TYPE_VOLUME_OVERLAY
//@hide 启动进度对话框, 在全局任何事物之上
TYPE_BOOT_PROGRESS
// 消费输入事件的窗口
TYPE_INPUT_CONSUMER
// @hide 屏保窗口, 在锁屏之上
TYPE_DREAM
// 导航条面板
TYPE_NAVIGATION_BAR_PANEL
// 显示覆盖窗口, 用于模拟第二个显示设备
TYPE_DISPLAY_OVERLAY
// 放大覆盖窗口 用于突出放大的部分
TYPE_MAGNIFICATION_OVERLAY
// @hide 锁屏scrim窗口, 当锁屏需要重启时显示
TYPE_KEYGUARD_SCRIM
// Presentation窗口
TYPE_PRIVATE_PRESENTATION
// @hide 语音互动窗口
TYPE_VOICE_INTERACTION
// 辅助功能覆盖层
TYPE_ACCESSIBILITY_OVERLAY
// @hide 语音互动开始窗口
TYPE_VOICE_INTERACTION_STARTING
// @hide 托盘窗口, 仅系统进程拥有
TYPE_DOCK_DIVIDER
// 类似TYPE_APPLICATION_ATTACHED_DIALOG, 但用于快速设置
TYPE_QS_DIALOG
// @hide 同TYPE_DREAM类似, 但用于截屏
TYPE_SCREENSHOT
```
# WindowManager.LayoutParams.flags
用于确定窗口的行为
```
// 当窗口对用户可见时, 允许锁屏.
// 可以单独使用, 也可以和FLAG_KEEP_SCREEN_ON和FLAG_SHOW_WHEN_LOCKED结合使用
FLAG_ALLOW_LOCK_WHILE_SCREEN_ON
// 该窗口以下的内容都会变暗 可以使用dimAmount来控制变暗的程度
FLAG_DIM_BEHIND
// 失效了, 不再支持 该窗口以下的内容都会模糊
FLAG_BLUR_BEHIND
// 该窗口不可获取按键输入焦点, 因此用户无法向其发送按键或按钮事件. 这些事件会被窗口以下的控件获取.
// 该flag同时会启动FLAG_NOT_TOUCH_MODAL, 无论你是否显式的设置
// 设置该flag同时暗示着该窗口不再需要同输入法交互, 因此该窗口和输入法窗口会以Z轴方式叠放(一般该窗口会覆盖在输入法窗口之上)
// 可以使用FLAG_ALT_FOCUSABLE_IM来修改这个行为
FLAG_NOT_FOCUSABLE
// 该窗口不可接收任何触摸事件
FLAG_NOT_TOUCHABLE
// 允许任何在该窗口之外的触摸事件传递到该窗口以下的控件, 即使该窗口是focusable的(即没有设置FLAG_NOT_FOCUSABLE).
// 否则该窗口会消费所有的触摸事件, 无论触摸是否在窗口之内
FLAG_NOT_TOUCH_MODAL
// 已经过时, 现在没有任何效果
// 如果设备处于睡眠中, 此时第一次点击屏幕的事件将会被该窗口接收
// 通常第一次触摸事件会被系统消费, 因为用户无法看见他们点击的是什么
FLAG_TOUCHABLE_WHEN_WAKING
// 当窗口对于用户可见时, 保持设备屏幕常亮
FLAG_KEEP_SCREEN_ON
// 将窗口放置在整个屏幕中, 忽略状态栏等周边装饰边框.
// 窗口内容必须定位正确, 才能获取到装饰边框信息
FLAG_LAYOUT_IN_SCREEN
// 允许窗口扩展到屏幕之外
FLAG_LAYOUT_NO_LIMITS
// 当该窗口显示时, 隐藏所有屏幕装饰(如状态栏), 允许窗口使用整个屏幕
// 当带有该flag的窗口是顶层窗口时, 状态栏会被隐藏
// 全屏窗口会忽略SOFT_INPUT_ADJUST_RESIZE对于softInputMode的值
// 窗口会一直保持全屏, 且不能缩放
// 可以通过theme属性来控制, 如Theme_Black_NoTitleBar_Fullscreen等
FLAG_FULLSCREEN
// 覆盖FLAG_FULLSCREEN, 并强制显示屏幕装饰(如状态栏)
FLAG_FORCE_NOT_FULLSCREEN
// 过时, 不再使用 开启图像抖动
FLAG_DITHER
// 将窗口内容作为安全内容, 阻止窗口出现在截屏, 或是被不安全的显示器显示
FLAG_SECURE
// 可以根据布局参数进行拉伸
FLAG_SCALED
// 用于在用户将屏幕贴近脸部时, 防止误按
FLAG_IGNORE_CHEEK_PRESSES
// 仅同FLAG_LAYOUT_IN_SCREEN一起使用.
// 窗口可能出现在装饰下面(如状态栏下面), 使用这个flag后, 窗口会确保不会被装饰物覆盖
FLAG_LAYOUT_INSET_DECOR
// 反转FLAG_NOT_FOCUSABLE的交互状态.
// 即, 如果同时设置了本flag和FLAG_NOT_FOCUSABLE, 则窗口表现为需要同输入法交互, 同时会被至于输入法之下
// 如果设置了本flag而没有设置FLAG_NOT_FOCUSABLE, 则窗口表现为不需要同输入法交互, 同时会被至于输入法之上
FLAG_ALT_FOCUSABLE_IM
// 如果设置了FLAG_NOT_TOUCH_MODAL, 那么可以同时设置此flag来接收窗口之外发生的
// MotionEvent.ACTION_OUTSIDE事件
// 注意, 你不会接收到完整的down/move/up手势, 只会接收到按下位置的ACTION_OUTSIDE事件
FLAG_WATCH_OUTSIDE_TOUCH
// 当锁屏时, 允许窗口显示
// 窗口优先于锁屏
// 可以同FLAG_KEEP_SCREEN_ON一起使用, 来保持屏幕常亮并在显示锁屏之前显示该窗口
/ 可以同FLAG_DISMISS_KEYGUARD一起使用, 来取消非安全的锁屏
// 该flag只能应用于最顶层的全屏窗口
FLAG_SHOW_WHEN_LOCKED
// 要求系统壁纸显示在窗口之下
// 窗口必须是透明的, 才可以看到壁纸
// 该flag只保证壁纸存在
// 可以通过theme属性来设置, 如Theme_Wallpaper_NoTitleBar等
FLAG_SHOW_WALLPAPER
// 当窗口被添加或从不可见到可见状态时, 会点亮屏幕
FLAG_TURN_SCREEN_ON
// 禁用锁屏, 除非是非安全锁屏
// 与FLAG_SHOW_WHEN_LOCKED正相反
// 如果锁屏当前是激活的, 并且是安全锁屏(需要解锁的), 那么用户仍需要进行解锁才能看到窗口, 除非设置了FLAG_SHOW_WHEN_LOCKED
FLAG_DISMISS_KEYGUARD
// 窗口会接收窗口之外的多点触摸事件
FLAG_SPLIT_TOUCH
// 对窗口启用硬件加速
FLAG_HARDWARE_ACCELERATED
// 允许窗口扩展到overscan区域
FLAG_LAYOUT_IN_OVERSCAN
// 要求状态栏透明
FLAG_TRANSLUCENT_STATUS
// 要求导航栏透明
FLAG_TRANSLUCENT_NAVIGATION
// 允许独立于window manager来控制焦点事件
// 通常该模式的窗口不能从window manager获取触摸/按键事件, 但能够通过
// Window#injectInputEvent(InputEvent)来获取本地注入事件
FLAG_LOCAL_FOCUS_MODE
// @hide
// 允许触摸从一个窗口划出到另一个窗口
// 该flag仅对当前窗口生效
// 触摸可以划出, 但无法再划入
FLAG_SLIPPERY
// 当布局依附于窗口时, 所依附的窗口可能会覆盖在屏幕装饰之上, 比如导航栏. 设置此flag后, window manager将在decor窗口内对所依附的窗口进行布局, 这样便不会覆盖在屏幕装饰上
FLAG_LAYOUT_ATTACHED_IN_DECOR
// 指示该窗口用于绘制系统状态栏的背景
// 如果设置此flag, 系统状态栏会变为透明背景, 窗口中响应的区域会被Window#getStatusBarColor()和Window#getNavigationBarColor()的颜色所填充
FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS
```
# WindowManager.LayoutParams.softInputMode
用于确定窗口和输入法之间的关系
```
// 指定输入法的覆盖层, 确定输入法区域是否可见的代码
SOFT_INPUT_MASK_STATE
// 输入法的可见状态为: 未指定状态
SOFT_INPUT_STATE_UNSPECIFIED
// 输入法的可见状态为: 不改变输入法当前状态
SOFT_INPUT_STATE_UNCHANGED
// 输入法的可见状态为: 用户进入窗口时, 隐藏所有输入法
SOFT_INPUT_STATE_HIDDEN
// 输入法的可见状态为: 窗口获取焦点时, 隐藏所有输入法
SOFT_INPUT_STATE_ALWAYS_HIDDEN
// 输入法的可见状态为: 用户进入窗口时, 显示输入法
SOFT_INPUT_STATE_VISIBLE
// 输入法的可见状态为: 当窗口获取输入焦点时, 显示输入法
SOFT_INPUT_STATE_ALWAYS_VISIBLE
// 指定窗口是否应该根据输入法进行调整的代码
SOFT_INPUT_MASK_ADJUST
// 窗口调整设置为: 未指定, 系统会尝试进行选择
SOFT_INPUT_ADJUST_UNSPECIFIED
// 窗口调整设置为: 当显示输入法时, 允许窗口被缩放, 使得窗口的内容不会被输入法覆盖
// 不能同SOFT_INPUT_ADJUST_PAN一起使用
// 如果窗口布局属性包含FLAG_FULLSCREEN, 该选项会被忽略, 窗口不会缩放, 而是保持全屏
SOFT_INPUT_ADJUST_RESIZE
// 窗口调整设置为: 当显示输入法时, 移动窗口使得输入焦点可见, 而不会缩放窗口
// 不能同SOFT_INPUT_ADJUST_RESIZE一起使用
SOFT_INPUT_ADJUST_PAN
// 窗口调整设置为: 当显示输入法时, 既不缩放, 也不移动
SOFT_INPUT_ADJUST_NOTHING
// 用户导航到此窗口时的配置代码. 通常有系统配置, 除非你需要自定义. 当窗口显示后, 该配置会清除
SOFT_INPUT_IS_FORWARD_NAVIGATION
```
# ActivityInfo.screenOrientation
用于确定窗口的方向
```
//不指定屏幕方向, 跟随系统
SCREEN_ORIENTATION_UNSPECIFIED
// 默认的横向(听筒在左, 按键在右)
SCREEN_ORIENTATION_LANDSCAPE
// 默认的竖向(听筒在上, 按键在下, 不包括听筒在下, 按键在上)
SCREEN_ORIENTATION_PORTRAIT
// 与默认相反的横向(听筒在右, 按键在左)
SCREEN_ORIENTATION_REVERSE_LANDSCAPE
// 与默认相反的竖向(实际和SCREEN_ORIENTATION_PORTRAIT一样)
SCREEN_ORIENTATION_REVERSE_PORTRAIT
// 重力传感器感知的方向(除听筒在下, 按键在上的3个方向)
SCREEN_ORIENTATION_SENSOR
// 不使用传感器方向
SCREEN_ORIENTATION_NOSENSOR
// 重力方向的横向(听筒在左, 按键在右 / 听筒在右, 按键在左)
SCREEN_ORIENTATION_SENSOR_LANDSCAPE
// 重力方向的竖向(听筒在上, 按键在下, 不包括听筒在下, 按键在上)
SCREEN_ORIENTATION_SENSOR_PORTRAIT
// 重力方向(听筒在上, 按键在下 / 听筒在下, 按键在上 / 听筒在左, 按键在右 / 听筒在右, 按键在左)
SCREEN_ORIENTATION_FULL_SENSOR
// 用户设置的方向
SCREEN_ORIENTATION_USER
// 用户设置的横向
SCREEN_ORIENTATION_USER_LANDSCAPE
// 用户设置的竖向
SCREEN_ORIENTATION_USER_PORTRAIT
// 用户设置的4个方向(听筒在上, 按键在下 / 听筒在下, 按键在上 / 听筒在左, 按键在右 / 听筒在右, 按键在左)
SCREEN_ORIENTATION_FULL_USER
// 当前界面下的Activity的方向
SCREEN_ORIENTATION_BEHIND
// 锁定当前方向
SCREEN_ORIENTATION_LOCKED
```
# 获取当前方向
```
int mCurrentOrientation = getResources().getConfiguration().orientation;
```
# WindowManager.LayoutParams.rotationAnimation
用于确定屏幕旋转时的动画. 是否有效取决于手机
```
// 立刻切换, 没有动画
ROTATION_ANIMATION_JUMPCUT
// 有淡入淡出效果
ROTATION_ANIMATION_CROSSFADE
// 旋转动画
ROTATION_ANIMATION_ROTATE
// (不知道)
ROTATION_ANIMATION_CHANGED
```
# params.dimAmount
窗口下层变暗程度, 默认1.0f不变暗
0.0f~1.0f

# buttonBrightness
设置按键的亮度. 有些手机没有键盘灯的无效
0.0f~1.0f

# screenBrightness
屏幕亮度
默认是负数, 表示跟随系统亮度
0.0f~1.0f表示最暗到最亮

# systemUiVisibility
设置系统界面的可见性. 是否有效和手机系统有关

# View.INVISIBLE
系统UI不可见
# View.SYSTEM_UI_FLAG_LOW_PROFILE
低调模式, 状态栏和图标会变暗

# 常见应用场景
## 悬浮窗
悬浮窗主要是设置type, 有多种type都可以实现悬浮窗效果(PHONE, SYSTEM_ALERT…)
```
WindowManager mWindowManager = (WindowManager) getApplicationContext().getSystemService(Context.WINDOW_SERVICE);
// 创建一个新的布局
WindowManager.LayoutParams param = new WindowManager.LayoutParams();
// 设置窗口属性
param.type = WindowManager.LayoutParams.TYPE_SYSTEM_ALERT; // 设置为系统警告窗, 可以悬浮在其他应用之上
param.format = PixelFormat.TRANSLUCENT; // 支持透明
param.flags = WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN // 可在全屏幕布局, 不受状态栏影响
| WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE; // 最初不可获取焦点, 这样不影响底层应用接收触摸事件
param.alpha = 0.9f; // 悬浮窗的透明度
param.gravity = Gravity.LEFT | Gravity.TOP; // 悬浮窗的重力效果
param.width = dp2px(140); // 悬浮窗宽度
param.height = WindowManager.LayoutParams.WRAP_CONTENT; // 悬浮窗高度
// 以下将悬浮穿定位在屏幕中央
int screenWidth = mWindowManager.getDefaultDisplay().getWidth();
int screenHeight = mWindowManager.getDefaultDisplay().getHeight();
param.x = (screenWidth - param.width) / 2;
param.y = (screenHeight - param.height) / 2;
// 创建悬浮窗view
mFloatView = View.inflate(this, R.layout.view_float_window, null);
ButterKnife.bind(this, mFloatView);
// 添加到屏幕
mWindowManager.addView(mFloatView, param);
```
# 悬浮窗中需要EditText进行输入
悬浮窗如果需要弹出输入法进行输入, 就需要获取焦点

**当悬浮窗中有EditText需要使用输入法时, 使用FLAG_NOT_TOUCH_MODAL, 这样悬浮窗中的EditText就可以弹出输入法.**

1. 此时下层应用可以操作, 但不能弹出输入法, 不能使用返回键.
2. 如需要恢复下层应用的完全操作, 可以等输入完毕后, 找合适时机再把悬浮窗改回FLAG_NOT_FOCUSABLE, 以便恢复背景window的控制

# 悬浮窗不影响下层应用的操作
比如在悬浮窗中没有EditText时, 使用FLAG_NOT_FOCUSABLE禁止获取焦点, 这样背景window就可以操作, 可以输入, 可以使用按键

# 悬浮窗和输入法之间的叠放效果
当输入法和悬浮窗输入窗口重叠时, 有两种处理方式, 都是通过param.softInputMode来实现:

1. 整体移动悬浮窗, 使输入的地方出现 WindowManager.LayoutParams.SOFT_INPUT_ADJUST_PAN
2. 缩小悬浮窗中可以缩小的部分, 使输入的地方出现 WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE
# 悬浮窗的拖动效果
为悬浮窗设置onTouchListener, 通过事件判断是点击还是移动
使用mWindowManager.updateViewLayout(mFloatView, param)来更新窗口位置
```
floatView.setOnTouchListener(new View.OnTouchListener() {
// 记录上次移动的位置
private float lastX = 0;
private float lastY = 0;
// 是否是移动事件
boolean isMoved = false;
@Override
public boolean onTouch(View v, MotionEvent event) {
switch (event.getAction()) {
case MotionEvent.ACTION_DOWN:
isMoved = false;
// 记录按下位置
lastX = event.getRawX();
lastY = event.getRawY();
break;
case MotionEvent.ACTION_MOVE:
isMoved = true;
// 记录移动后的位置
float moveX = event.getRawX();
float moveY = event.getRawY();
// 获取当前窗口的布局属性, 添加偏移量, 并更新界面, 实现移动
WindowManager.LayoutParams param = (WindowManager.LayoutParams)                                 mFloatView.getLayoutParams();
param.x += (int) (moveX - lastX);
param.y += (int) (moveY - lastY);
mWindowManager.updateViewLayout(mFloatView, param);
lastX = moveX;
lastY = moveY;
case MotionEvent.ACTION_CANCEL:
isMoved = true;
break;
}
// 如果是移动事件, 则消费掉; 如果不是, 则由其他处理, 比如点击
return isMoved;
}
});
```
# Activity设置屏幕旋转, 全屏等
```
android:configChanges="orientation|screenSize"
keyboardHidden
orientation
screenSize
orientation|screenSize: 改变屏幕方向时并不重启Activity

setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE)
getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
getWindow().setAttributes(windowParams);
```
