# 前言
> 最近在搞即时语音聊天的功能，经过几个月的努力，目前基本稳定，达到了可以上线的标准，在此写篇博客记录下。
客户端采用rtmp协议做推流和拉流，在网络稳定的情况下，实测延迟在200ms～500ms。

# RTMP/RTSP协议说明
> RTMP仅支持TCP协议、RTSP支持TCP和UDP两种协议。针对推流端，不管使用RTMP还是RTSP都需要使用TCP协议，以保证源头数据的正确性。拉流端如果使用RTSP的话，可以使用UDP协议。

**使用RTMP作为拉流端的问题**
> 在网络状态变化的时候，可能导致拉流端声音来不及消费，使缓存区数据过多，从而使延迟变得严重。解决办法即：需要判断缓存区已缓存的大小，计算出需要消费的时间，根据自己的需要，在超出预期的时候，断开重连。RTMP只支持TCP协议，如果不断开重连，数据不会丢失，必定要消费掉，延迟没法解决。

# 声音录制
## 初始化AudioRecord
```
int bufferSize = AudioRecord.getMinBufferSize(sampleRate,AudioFormat.CHANNEL_IN_MONO,AudioFormat.ENCODING_PCM_16BIT);
audioRecord = new AudioRecord(MediaRecorder.AudioSource.VOICE_COMMUNICATION, sampleRate,
AudioFormat.CHANNEL_IN_MONO, AudioFormat.ENCODING_PCM_16BIT, bufferSize);
```
## 开始录制
```
audioRecord.startRecording();
```
## 获取音频流
```
int bufferReadResult;
byte[] data = new byte[bufferSize];
while (isRecording() && (bufferReadResult = audioRecord.read(data, 0, bufferSize)) > 0) {
listener.onAudioRecorded(data, bufferReadResult);
}
```
```
boolean isRecording() {
return audioRecord != null
&& audioRecord.getRecordingState() == AudioRecord.RECORDSTATE_RECORDING;
}
```
## 停止录制
```
audioRecord.release();
```

# 音频参数
> 从初始化AudioRecord中可以看出，我们需要掌握的概念有4个：
采样率（sampleRate）
声道（AudioFormat.CHANNEL_IN_MONO）
采样位数（AudioFormat.ENCODING_PCM_16BIT）
声源类型（MediaRecorder.AudioSource.VOICE_COMMUNICATION）。

## 采集

### 采样率
```
private int sampleRate = 44100;   
```
> 录音设备在一秒钟内对声音信号的采样次数，采样频率越高声音的还原就越真实越自然。在当今的主流采集卡上，采样频率一般共分为11025Hz、22050Hz、24000Hz、44100Hz、48000Hz五个等级，11025Hz能达到AM调幅广播的声音品质，而22050Hz和24000HZ能达到FM调频广播的声音品质，44100Hz则是理论上的CD音质界限，48000Hz则更加精确一些。

### 声道
> 常有单声道和立体声之分，单声道的声音只能使用一个喇叭发声（有的也处理成两个喇叭输出同一个声道的声音），立体声可以使两个喇叭都发声（一般左右声道有分工） ，更能感受到空间效果，当然还有更多的通道数。

```
CHANNEL_IN_DEFAULT = 1;
CHANNEL_IN_LEFT = 0x4;
CHANNEL_IN_RIGHT = 0x8;
CHANNEL_IN_FRONT = 0x10;
CHANNEL_IN_BACK = 0x20;
CHANNEL_IN_LEFT_PROCESSED = 0x40;
CHANNEL_IN_RIGHT_PROCESSED = 0x80;
CHANNEL_IN_FRONT_PROCESSED = 0x100;
CHANNEL_IN_BACK_PROCESSED = 0x200;
CHANNEL_IN_PRESSURE = 0x400;
CHANNEL_IN_X_AXIS = 0x800;
CHANNEL_IN_Y_AXIS = 0x1000;
CHANNEL_IN_Z_AXIS = 0x2000;
CHANNEL_IN_VOICE_UPLINK = 0x4000;
CHANNEL_IN_VOICE_DNLINK = 0x8000;
CHANNEL_IN_MONO = CHANNEL_IN_FRONT;
CHANNEL_IN_STEREO = (CHANNEL_IN_LEFT | CHANNEL_IN_RIGHT);
CHANNEL_IN_FRONT_BACK = CHANNEL_IN_FRONT | CHANNEL_IN_BACK;
```
通常使用CHANNEL_IN_MONO作为单声道，CHANNEL_IN_STEREO作为双声道。
### 采样位数
> 即采样值或取样值（就是将采样样本幅度量化）。它是用来衡量声音波动变化的一个参数，也可以说是声卡的分辨率。它的数值越大，分辨率也就越高，所发出声音越精细。
每个采样数据记录的是振幅, 采样精度取决于采样位数的大小:
1 字节(也就是8bit) 只能记录 256 个数, 也就是只能将振幅划分成 256 个等级;
2 字节(也就是16bit) 可以细到 65536 个数, 这已是 CD 标准了;

```
ENCODING_INVALID = 0;
ENCODING_DEFAULT = 1;

ENCODING_PCM_16BIT = 2;
ENCODING_PCM_8BIT = 3;
ENCODING_PCM_FLOAT = 4;

ENCODING_AC3 = 5;
ENCODING_E_AC3 = 6;

ENCODING_DTS = 7;
ENCODING_DTS_HD = 8;

ENCODING_MP3 = 9;

ENCODING_AAC_LC = 10;
ENCODING_AAC_HE_V1 = 11;
ENCODING_AAC_HE_V2 = 12;
```
通常使用PCM的16BIT作为采样位数，能满足基本需求。

### 音频源类型
> 这里主要针对手机，主要手机端根据应用使用的场景，听过多种音频源类型以供选择，当然用户也可以使用纯麦克风，未经过任何处理的数据源。手机端提供多种音频源类型，关于降噪的，需要先了解手机的麦克风：

** 麦克风背景 **
> 早些年的手机都是一个麦克风，一般都在我们的手机下端充电口附近，但是一个麦克风通话时候容易有噪音，影响我们的通话质量。
但是最近这几年，由于电子行业的迅速崛起和竞争，手机制造商们也是想尽了办法来制造更高品质的手机，麦克风已经加到了两个，大家可以看下自己的手机听筒的上方或者顶部是不是也有一个小孔，那么这个孔就是第二个麦克风的位置，一般我们打电话的时候都用的是手机下方的麦克风，当我们打开免提或者视频通话的时候用的就是上方的麦克，以后对方听不见可不要傻傻的对着下方的麦克风大声讲话了。
还有iphone手机为了通话质量的提升竟然做了三个麦克风，第三个一般都在后置摄像头闪光灯附近，一般都是录音用，这么多麦克风，各有各的功能，通话降噪方面起到了不小的作用！
通常情况下屏幕底部的小孔是主麦克风，屏幕顶部的麦克风是降噪麦克风。因为主麦克风主要用于拾取通话声音，所以靠近人体的嘴巴位置，而降噪麦克风则需要与主麦克风保持一定的距离，通常放在屏幕的顶部，远离嘴巴位置。通话时，人体发音源嘴巴与主麦克风、降噪麦克风的距离不同，也就导致两个麦克风接收到的人声强度有所不同（大约相差6dB）。另一方面，两个麦克风接收到的环境噪音强度近似相同。降噪麦克的信号经过手机内部做声音相位反相处理就可以和主麦克风抵消环境噪音带来的影响，保证通话质量。一般经过双MIC降噪技术的处理即使身处在闹市之中通话质量依然杠杠滴。相反，如果没有这个技术处理的话传输出去的声音就会很嘈杂通话质量自然就会大大的降低。
![](leanote://file/getImage?fileId=5d7f2a54e2a5d70f61000000)

** 安卓手机提供流如下声源类型供选择（真实如何，同时需要考虑各厂家手机是否支持，以及其硬件特殊性和处理方法）： **

```
// 默认的音频源
DEFAULT
// 手机默认音频源
MIC
// 语音呼叫上行（TX）音频源。（需要权限，且权限只有系统app获取到）
VOICE_UPLINK
// 语音呼叫下行链路（RX）音频源。（需要权限，且权限只有系统app获取到）
VOICE_UPLINK
// 语音通话上行+下行音频源 （需要权限，且权限只有系统app获取到）
VOICE_CALL
// 麦克风音频源可调谐为视频录制，与相机的方向相同（如果可用）
CAMCORDER
// 为语音识别调谐的麦克风音频源
VOICE_RECOGNITION
// 为语音通信（如VoIP）调谐的麦克风音频源。例如，它将利用回声抵消或自动增益控制（如果可用）
VOICE_COMMUNICATION
// 用于远程呈现音频流的亚文本的音频源。应用程序可以使用此音频源捕获音频流的混合，应该传输到远程接收器，如WiFi显示器。当录音处于活动状态时，这些音频流被重定向到远程提交，而不是在设备扬声器或耳机上播放。（需要权限，且权限只有系统app获取到）
REMOTE_SUBMIX
// 为未处理（原始）声音调谐的麦克风音频源（如果可用）的行为类似于{@Link #DEFAULT}
UNPROCESSED
// 用于捕获广播无线电调谐器输出的音频源。（隐藏方法，只供底层调用）
RADIO_TUNER
// 用于可抢占、低优先级软件热词检测的音频源它提供与{@link语音识别}相同的增益和预处理调谐。应用程序应该在希望使用此音频源时使用此音频源，始终打开软件Hotword检测，同时优雅地向任何其他应用程序屈服。可能想从麦克风里读出来。
（隐藏方法，只供底层调用，且需要权限）
HOTWORD
```
> 排除不可用的，可以供手机使用的也只有DEFAULT、MIC、CAMCORDER、VOICE_RECOGNITION、VOICE_COMMUNICATION。通常开发默认情况使用MIC。录制视频可以使用CAMCORDER，语音识别使用VOICE_RECOGNITION，自动降噪使用VOICE_COMMUNICATION。另外，在手机有上下两个麦克风的情况下，使用VOICE_COMMUNICATION会使用上方的麦克风，使用MIC则使用下方的麦克风。

# 录音时屏蔽扬声器
```
AudioManager audioManager = (AudioManager) context.getSystemService(Context.AUDIO_SERVICE);
audioManager.setSpeakerphoneOn(false);
// 可以使用如下方法判断是否开启扬声器
boolean isOpenSpeaker = audioManager.isSpeakerphoneOn();
```

## 设置播放声音类型
> 在很多应用、游戏中，明明播放的是媒体音乐，调整音量的时候发现调的是通话声音，怎么做到的？请看下面：
```
// 摘自博客 https://www.cnblogs.com/loveflycforever/p/4881945.html
// 获取AudioManager实例对象
AudioManager audioManage = (AudioManager) context.getSystemService(Context.AUDIO_SERVICE);
// 获取最大音量和当前音量，参数：STREAM_VOICE_CALL（通话）、STREAM_SYSTEM（系统声音）、STREAM_RING（铃声）、STREAM_MUSIC（音乐）和STREAM_ALARM（闹铃）
int max = audioManager.getStreamMaxVolume(int streamType);
int current = audioManager.getStreamVolume(int streamType);
// 获取当前的铃声模式，返回值：RINGER_MODE_NORMAL（普通）、RINGER_MODE_SILENT（静音）或者RINGER_MODE_VIBRATE（震动）
int rMode = audioManager.getRingerMode();
// 获取当前音频模式，返回值：MODE_NORMAL（普通）、MODE_RINGTONE（铃声）、MODE_IN_CALL（呼叫）或者MODE_IN_COMMUNICATION（通话）
int mode = audioManager.getMode();

// 设置音量大小，第一个参数：STREAM_VOICE_CALL（通话）、STREAM_SYSTEM（系统声音）、STREAM_RING（铃声）、STREAM_MUSIC（音乐）和STREAM_ALARM（闹铃）；第二个参数：音量值，取值范围为0-7；第三个参数：可选标志位，用于显示出音量调节UI（AudioManager.FLAG_SHOW_UI）。
audioManager.setStreamVolume(int streamType, int index, int flags);
// 设置铃声模式，参数：RINGER_MODE_NORMAL（普通）、RINGER_MODE_SILENT（静音）或者RINGER_MODE_VIBRATE（震动）
audioManager.getRingerMode(int ringerMode);
// 设置音频模式，参数：MODE_NORMAL（普通）、MODE_RINGTONE（铃声）、MODE_IN_CALL（呼叫）或者MODE_IN_COMMUNICATION（通话）
audioManager.setMode(int mode);
// 设置静音/取消静音，第二个参数：请求静音状态，true（静音）false（取消静音）
audioManager.setStreamMute (int streamType, boolean state);

// 调节手机音量大小，第二个参数：调整音量的方向，可取ADJUST_LOWER（降低）、ADJUST_RAISE（升高）、ADJUST_SAME（不变）。
audioManager.adjustStreamVolume(int streamType, int direction, int flags);

```
> 以上说明了各种声音类型的设置和获取。
但在使用是依然还有些需要注意的地方：
1.  setMode()，普通app只能设置MODE_NORMAL，MODE_RINGTONE，MODE_IN_COMMUNICATION三个，且需要在AndroidManifest里面添加 android.permission.MODIFY_AUDIO_SETTINGS 权限。MODE_IN_CALL只有系统app可以设置。
2. setMode()设置为MODE_NORMAL、MODE_RINGTONE，调节的是媒体音量；设置为MODE_IN_COMMUNICATION之后，调节的则为通话音量。




