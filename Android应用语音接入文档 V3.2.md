# Android应用语音接入文档

## Develop Tool: Android Studio
## SDK说明：
- 本SDK支持两种语音模式：
    - 实时语音
		两人都进入同一个房间，就可以像打电话那样聊天，支持多人进入同一个房间
    - 离线语音
		如同微信发语音那样，录制一段语音发送到服务器，服务器返回此语音在服务器的地址，
		然后用户可将此地址发送给其他用户达到语音交流的效果
- 本SDK支持视频聊天，可直播


## 下载sdk并解压
> [下载SDK](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_zip/RTChatSDK_android_release_3.0.zip)

---
## 项目配置
- 加入libs
	创建Android Studio项目：ASDemo，
	把解压的后获取的文件放到ASDemo/app/libs中，然后Add as Library,如图：
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/addlibrary.png" width="430">

```xml
其中support v4包是供unity等使用的，Android studio推荐使用gradle配置：
app/build.gradle 的dependencies标签中：
compile 'com.android.support:appcompat-v7:25.3.0'
```

- 配置so库的加载

```xml
	在app/build.gradle的android标签中配置：
	sourceSets{
	  main{
	      jniLibs.srcDir("libs")
	  }
	}
```

- 配置权限：
```xml
AndroidManifest.xml配置：
	<uses-permission android:name="android.permission.CAMERA"/>
	<uses-permission android:name="android.permission.RECORD_AUDIO"/>
	<uses-permission android:name="android.permission.INTERNET"/>
	<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
	<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS"/>
	<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
	<uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
	<uses-permission android:name="android.permission.WAKE_LOCK"/>
	<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
	<uses-permission android:name="android.permission.WRITE_SETTINGS"/>
	<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
	<uses-permission android:name="android.permission.BLUETOOTH"/>
	<uses-permission android:name="android.permission.CAPTURE_VIDEO_OUTPUT"/>
	<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
	<uses-permission android:name="android.permission.READ_CONTACTS"/>
```


***
## 接入：
### 以下接口都应该在主线程中调用
- 注册，传入上下文context：
```java
    函数原型：
	public void register(Activity activity);
```
参数说明：
参数|类型|意义
:-:|:-:|:-:
activity|android.app.Activity|当前的activity


- 初始化sdk:
```java
主线程中调用
函数原型：
    public void InitSDK(String appId, String key);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
appId|String|官网上申请的appId
key|String|官网上申请的key

- 设置用户名与用户key:
```java
主线程中调用
函数原型：
    public native int SetUserInfo(String username, String userkey);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
username|String|用户名
userkey|String|用户key

- 设置房间信息:
```java
函数原型：
     public native int SetRoomType(int media_type, int layout_mode);
```
参数|类型|说明
:-:|:-:|:-:
media_type|int|媒体类型，语音值为1，具体值见以下说明
layout_mode|int|为视频接口使用，用来确定可同时查看多少人的视频，语音随意填值
```xml
media_type的值及含义：
	    enum enMediaType
    {
        kVoiceOnly = 0x01,
        kVideoOnly = 0x02,
        kVoiceAndVideo = 0x03,
    };
    
    enum enMediaProperty
    {
        kVoiceLowMark = 0x00,
        kVoiceMediumMark = 0x20,
        kVoiceHighMark = 0x40,
        kMusicLowMark = 0x60,
        kMusicMediumMark = 0x80,
        kMusicHighMark = 0xA0,
        
        kVideoSmall_normalDefinition = 0x00,
        kVideoSmall_highDefinition = 0x800,
        kVideoSmall_veryHighDefinition = 0x1000,
        kVideoMedium_normalDefinition = 0x1800,
        kVideoMedium_highDefinition = 0x2000,
        kVideoMedium_veryHighDefinition = 0x2800,
        kVideoLarge_normalDefinition = 0x3000,
        kVideoLarge_highDefinition = 0x3800,
        kVideoLarge_veryHighDefinition = 0x4000
    };
    
    enum enConferenceProperty
    {
        kConferenceNormal = 0x00,
        kConferenceNeedForward = 0x100,
        kConferenceNeedMix = 0x200,
    };
```

- 进入房间：
```java
主线程中调用
函数原型：
    public native int RequestJoinRoom(String roomId);
```

- 打开/关闭扬声器：
```java
主线程中调用，进入房间之后调用才有效
函数原型：
    public native int UseLouderSpeaker(boolean enable);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
enable|boolean| true:打开扬声器；false:关闭扬声器

- 调节音量：
```java
主线程中调用，进入房间之后调用才有效
函数原型：
    public native int AdjustSpeakerVolume(float volumeValue);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
	volumeValue|float|音量数值，范围0-10

- 离开房间：
```java
主线程中调用
函数原型：
		public native int RequestQuitRoom();
```

- 录音
```java
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
函数原型：
    public native boolean StartIMRecord(boolean needConvertWord, boolean isPersis, float scaleLevel);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
needConvertWord|boolean|是否需要将录音翻译成文字，true:翻译成文字
isPersis|boolean|是否需要将录音文件永久保存，true:永久保存
scaleLevel|float|录制声音的放大倍率，默认1.0，用户可根据需求调整


- 停止录音
```java
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
函数原型：
  public native boolean StopIMRecord();
```


- 取消录音：
```java
主线程中调用
函数原型：
    public native boolean cancelIMRecord();
```
- 播放录音
```java
主线程中调用
函数原型：
    public native boolean StartPlayIMVoice(String filepath);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
filePath|String|录音完后自动上传回调返回来的录音文件地址url


- 停止播放录音
```java
主线程中调用
函数原型：
	public native boolean StopPlayIMVoice();
```

- 反注册
```java
主线程中调用，当不使用本sdk时调用，一般在onDestroy中调用，
函数原型：
	public native void unRegister();
```
---
## 回调：
- 回调的必要性：
	只有当初始化完成后才能调用进入房间的接口;
	只有进入房间成功后，才能调用调节音量、打开/关闭扬声器;
	只有录音结束、上传完成后才能调用播放录音的接口.

- 原型：
```java
	public void SdkListener(int cmdType, final int error, String dataPtr, int dataSize)
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
cmdType|int|7:进入房间 25:录音结束 35:播放结束
error|int|1代表成功，0代表失败
dataPtr|String|返回的字符串数据，json格式
dataSize|int|返回的字符串数据大小

- 回调示例：
```java
receiveDataFromC = new ReceiveDataFromC();
receiveDataFromC.set_voice_listener(new SdkVoiceListener() {
    @Override
    public void SdkListener(int cmdType, final int error, String dataPtr, int dataSize) {
        Log.i(TAG, "-回调到MainActivity中-jni_log----cmdType:" + cmdType + " error:" + error + " dataPtr:" + dataPtr + " dataSize:" + dataSize);
        switch (cmdType) {
            case 1://初始化
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(MainActivity.this, "初始化完毕" + error, 0).show();
                    }
                });
                Log.i(TAG, "-MainActivity-初始化完毕------lala" + error);
                break;
            case 7://进入房间
                Log.i(TAG, "-MainActivity-joinRoom------lala" + error);
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(MainActivity.this, "进入房间" + error, 0).show();
                    }
                });
                break;
            case 25://录音结束，上传成功
                FileData fileData = getDataFromJson(dataPtr);
                String voiceText;
                float duration;
                if (fileData == null) {
                    downloadUrlLocal = null;
                    voiceText = null;
                    duration = 0;
                } else {
                    downloadUrlLocal = fileData.getUrl();
                    voiceText = fileData.getText();
                    duration = Float.valueOf(fileData.getDuration());
                }
                Log.i(TAG, "-录音结束-jni_log----error:" + error + " downloadUrlLocal:" + downloadUrlLocal + " duration:" + duration + " text:" + voiceText);
                voiceTextLocal = voiceText;
                etResult.setText("回调后的翻译：" + voiceTextLocal);
                break;
            case 35://播放结束
                Toast.makeText(MainActivity.this, "播放完毕", 0).show();
                Log.i(TAG, "-MainActivity-播放完毕------error" + error + " " + dataSize);
                break;
        }
    }
});
```
---
## 视频接入

- 发送本地视频：
```java
主线程中调用，进入房间之后调用才有效
	mNVEngine.StartSendVideo();
函数原型：
	public native int StartSendVideo();
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
返回值|int| 0 失败, 1 成功

- 显示本地视频：
```java
主线程中调用，进入房间之后调用才有效
	mNVEngine.ObserverLocalVideoWindow(isChecked,glLocalSurfaceViewContainer.getSurfaceView());
函数原型：
  public native int ObserverLocalVideoWindow(boolean enable, SurfaceView view);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
enable|boolean|true:打开, false:关闭
view|SurfaceView|本地视频渲染窗口
返回值|int|0 失败, 1 成功

- 接收远端视频：
```java
主线程中调用，进入房间之后调用才有效
	mNVEngine.ObserverRemoteTargetVideoV1("",glRemoteSurfaceViewContainer.getSurfaceView());
函数原型：
	public native int ObserverRemoteTargetVideoV1(String target,SurfaceView view);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
view|SurfaceView|远端视频渲染窗口
target|String| 传入的字符是用户名，用以查看指定用户的视频，空字符串代表不看远端视频
返回值|int|0 失败, 1 成功

- 关闭远端视频：
```java
主线程中调用
	mNVEngine.ObserverRemoteTargetVideoV1("",null);
函数原型：
	public native int ObserverRemoteTargetVideoV1(String target,SurfaceView view);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
view|SurfaceView|远端视频渲染窗口
target|String| 传入的字符是用户名，用以查看指定用户的视频，空字符串代表不看远端视频
返回值|int|0 失败, 1 成功

- 切换前后摄像头：
```java
主线程中调用，进入房间之后调用才有效
	mNVEngine.SwitchVideoSource(enVideoSource.kVideoSourceBackCamera.ordinal());
函数原型：
	public native int SwitchVideoSource(int videoSource);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
videoSource|int| 1:前置摄像头, 2:后置摄像头
返回值|int|0 失败, 1 成功

- 开始屏幕分享：
```java
主线程中调用，进入房间之后调用才有效，目前仅支持Android5.0 及以上
	VideoEngineImpl.getInstance().startScreenCapture(this);
函数原型：
	public boolean startScreenCapture(Activity context)
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
context|Activity| 上下文
返回值|boolean| true:成功；false:失败


- 打开美颜：
```java
主线程中调用，进入房间之后调用才有效
	mNVEngine.EnableBeautifyFilter(isBeautify);
函数原型：
	public void EnableBeautifyFilter(final boolean enabled)
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
enabled|boolean| true:打开美颜；false:关闭美颜

- 变声：
```java
主线程中调用，进入房间之后调用才有效
	NativeVoiceEngine.getInstance().SetVoiceChangeParam(result,reverbLevel);
函数原型：
	public native int SetVoiceChangeParam(int pitch, int reverbLevel);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
pitch|int|pitch 取值范围为 [-10,10]
reverbLevel|int|取值范围为[0 ~ 9], 0 为原始声音，1到9，混响级别逐渐增强
