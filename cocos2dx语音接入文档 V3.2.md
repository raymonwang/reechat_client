# cocos2dx 接入文档

## SDK说明：
- 本SDK支持两种语音模式：
  - 实时语音
		两人都进入同一个房间，就可以像打电话那样聊天，支持多人进入同一个房间
  - 离线语音
		如同微信发语音那样，录制一段语音发送到服务器，服务器返回此语音在服务器的地址，
		然后用户可将此地址发送给其他用户达到语音交流的效果
- 其他：
  ios编译暂时不支持模拟器，需要真机编译调试

## 下载SDK
> [Cocos2d语音SDK](http://www.reechat.org/)

---

## 系统配置  
- iOS系统配置

>对于iOS的Xcode工程，引入ios sdk的framework文件

<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_ios_1.png" width="430">

>确认工程的Build Settings->Other Linker Flags下有-ObjC配置，如无请自行添加
注:请在Build Phases下检查是否有增加了如下静态链接库，如无请添加

<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_ios_2.png" width="430">

- Android 系统配置

- Eclipse配置：
 >把libs/Android/下文件放到proj.android/libs目录下。然后include和libs/Android目录放到合适的目录，比如工程下面建一个sdk目录：

在proj.anroid/jni的Android.mk中添加对库文件和头文件的引用，注意如下几个位置：    
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_1.png" width="430">
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_2.png" width="430">
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_3.png" width="430">

在proj.android/AndroidManifest.xml添加如下权限即可按照Cocos的编译方式    
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_4.png" width="430">  
最后需要在Java中初始化，比如：  
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_5.png" width="430">

- Android Studio配置：
```xml
  在app目录下创建文件夹libs，将下载的libs中的Android中的文件拷到libs目录下，并Add AS library
  配置Android.mk
    在app/jni/Android.mk中添加对库文件和头文件的引用：
```
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_1.png" width="430">
<br/>
<br>
<img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/cocos_android_2.png" width="430">
<br/>

```java
配置 Application.mk:
    在app/jni/Application.mk中配置对ABI的支持：
    APP_STL := gnustl_static

    APP_CPPFLAGS := -frtti -DCC_ENABLE_CHIPMUNK_INTEGRATION=1 -std=c++11 -fsigned-char
    APP_LDFLAGS := -latomic

    APP_ABI := armeabi-v7a arm64-v8a x86

    ifeq ($(NDK_DEBUG),1)
      APP_CPPFLAGS += -DCOCOS2D_DEBUG=1
      APP_OPTIM := debug
    else
      APP_CPPFLAGS += -DNDEBUG
      APP_OPTIM := release
    endif

AndroidManifest.xml 中添加权限：

    <!-- 语音sdk权限开始 -->
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!-- 语音sdk权限结束 -->

在启动Activity中添加初始化：

    public class AppActivity extends Cocos2dxActivity {
        private NativeVoiceEngine nativeVoiceEngine = new NativeVoiceEngine();

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            // TODO Auto-generated method stub
            super.onCreate(savedInstanceState);
            nativeVoiceEngine.register(this);
        }
    }
```





---

## 基本API
### 下面所有接口需要主线程调用

- 初始化sdk:
```Objective-C
主线程中调用，不可重复初始化
  ReeChatMain::sharedInstance().InitSDK(appid, appkey);
用于初始化SDK


函数原型：
  int InitSDK(const char* appid, const char* key);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
appId|String|官网上申请的appId
key|String|官网上申请的key

- 设置用户信息:
```Objective-C
主线程中调用
  ReeChatMain::sharedInstance().SetUserInfo(username, "32261be4ed6fd0d90976da1f7a85237d");
函数原型：
  int SetUserInfo(const char* username, const char* userkey);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
username|String|用户名
userkey|String|用户key

- 设置房间信息:
```Objective-C
ReeChatMain::sharedInstance().SetRoomType(kVoiceAndVideo|kMusicLowMark|kVideoLarge_veryHighDefinition|kConferenceNeedMix, 4);
函数原型：
    void SetRoomType(int media_type, int layout_mode);
```
参数说明：
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
```Objective-C
主线程中调用
  ReeChatMain::sharedInstance().RequestJoinRoom(roomid);
函数原型：
  int RequestJoinRoom(const char* roomid);
```


- 打开/关闭扬声器：
```Objective-C
主线程中调用，进入房间之后调用才有效
  ReeChatMain::sharedInstance().UseLoudSpeaker(enable);
函数原型：
  int UseLoudSpeaker(bool enable);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
enable|boolean| true:打开扬声器；false:关闭扬声器


- 调节音量：
```Objective-C
主线程中调用，进入房间之后调用才有效
  ReeChatMain::sharedInstance().adjustSpeakerVolume(value);
函数原型：
  int adjustSpeakerVolume(float volume);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
	volumeValue|float|音量数值，范围0-10


- 离开房间：
```Objective-C
主线程中调用
	ReeChatMain::sharedInstance().RequestQuitRoom();
函数原型：
	int RequestQuitRoom();
```


- 录音
```Objective-C
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
  ReeChatMain::sharedInstance().StartIMRecord(true,true,1);
函数原型：
  int StartIMRecord(boolean needConvertWord,boolean isPersis, float scaleLevel);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
needConvertWord|boolean|是否需要将录音翻译成文字，true:翻译成文字
isPersis|boolean|是否需要将录音文件永久保存，true:永久保存
scaleLevel|float|录制声音的放大倍率，默认1.0，用户可根据需求调整


- 停止录音
```Objective-C
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
  ReeChatMain::sharedInstance().StopIMRecord();
函数原型：
  int StopIMRecord();
```


- 取消录音：
```Objective-C
主线程中调用
  ReeChatMain::sharedInstance().CancelIMRecord();
函数原型：
  int CancelIMRecord();
```

- 播放录音
```Objective-C
主线程中调用
  ReeChatMain::sharedInstance().StartPlayIMVoice([[dicParam valueForKey:@"url"] UTF8String]);
函数原型：
  int StartPlayIMVoice(const char* voiceUrl);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
filePath|String|录音完后自动上传回调返回来的录音文件地址url


- 停止播放录音
```Objective-C
主线程中调用
  ReeChatMain::sharedInstance().StopPlayIMVoice();
函数原型：
	int StopPlayIMVoice();
```
