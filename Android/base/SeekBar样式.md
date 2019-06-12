
# 使用说明
### 布局中定义Seekbar
    <!-- SeekBar -->
    <SeekBar
        android:id="@+id/set_voice_seekbar"
        style="@style/SetSeekBar"
        android:layout_centerVertical="true"
        android:layout_toLeftOf="@+id/set_voice_text"
        android:thumbOffset="3dp" />
        
### 样式
    <!-- Style -->
    <style name="SetSeekBar">
        <item name="android:layout_width">500dp</item>
        <item name="android:layout_height">wrap_content</item>
        <item name="android:layout_centerInParent">true</item>
        <item name="android:layout_marginRight">30dp</item>
        <item name="android:progressDrawable">@drawable/seekbar_style</item>
        <item name="android:thumb">@mipmap/ic_set_voice_thumb</item>
    </style>
    
### progressDrawable样式定义
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
        <!-- 背景 -->
        <item
            android:id="@android:id/background"
            android:drawable="@drawable/set_seekbar_bg" />
        <!-- 进度条 -->
        <item android:id="@android:id/progress">
            <clip android:drawable="@drawable/set_seekbar_after" />
        </item>
    </layer-list>
