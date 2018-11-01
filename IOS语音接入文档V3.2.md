# IOS 应用语音接入文档

## Develop Tool Xcode
## SDK说明：
- 本SDK支持两种语音模式：
  - 实时语音
		两人都进入同一个房间，就可以像打电话那样聊天，支持多人进入同一个房间
	- 离线语音
		如同微信发语音那样，录制一段语音发送到服务器，服务器返回此语音在服务器的地址，
		然后用户可将此地址发送给其他用户达到语音交流的效果
- 本SDK支持视频聊天，可直播
- 其他：
  暂时不支持模拟器，需要真机编译调试

##下载sdk并解压
> [下载SDK](http://www.reechat.org/)


---
## 项目配置
- 配置静态库：
```xml
1.导入全部静态库和thirdpard目录下的文件到xcode工程。
2.导入include文件夹下的头文件到xcode源码目录。
3.确认工程的Build Settings->Other Linker Flags下有-ObjC配置，如无请自行添加
注:请在Build Phases下检查是否有增加了如下静态链接库，如无请添加
```
![image](https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/ios_link.png)

- 配置权限：
```xml
  检查录音和使用摄像头权限，请在info.plist文件中添加如下两个键值，
  Privacy – Microphone Usage Description
  Privacy – Camera Usage Description
```

***
## 接入：
### 下面接口都需要在主线程调用
- 注册回调：
```Objective-C
  ReeChatMain::sharedInstance().RegisterCallback(sdkCallBack);
	用于注册回调，需要在InitSDK之前调用
	只有当初始化完成后才能调用进入房间的接口;
	只有进入房间成功后，才能调用调节音量、打开/关闭扬声器;
	只有录音结束、上传完成后才能调用播放录音的接口.

```

- 设置自定义参数
```Objective-C
ReeChatMain::sharedInstance().SetSdkParam("RoomServerAddr", "gateway.reechat.org:8080");
用于设置自定义参数，如网关服务器地址
```

- 初始化sdk:
```Objective-C
  //不可重复初始化
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
  ReeChatMain::sharedInstance().SetUserInfo(username, "");
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
  ReeChatMain::sharedInstance().AdjustSpeakerVolume(value);
函数原型：
  int AdjustSpeakerVolume(float volume);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
	volumeValue|float|音量数值，范围0-10

- 离开房间：
```Objective-C
	ReeChatMain::sharedInstance().RequestQuitRoom();
函数原型：
	int RequestQuitRoom();
```

- 录音
```Objective-C
  //进入房间之后无法调用，必须离开房间或未进入房间
  ReeChatMain::sharedInstance().startRecordVoice(true,true,1);
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
  //进入房间之后无法调用，必须离开房间或未进入房间
  ReeChatMain::sharedInstance().StopIMRecord();
函数原型：
  int StopIMRecord();
```


- 取消录音：
```Objective-C
  ReeChatMain::sharedInstance().CancelIMRecord();
函数原型：
  int CancelIMRecord();
```


- 播放录音
```Objective-C
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
  ReeChatMain::sharedInstance().StopPlayIMVoice();
函数原型：
	int StopPlayIMVoice();
```

---
## 视频接入
- 发送本地视频：
```Objective-C
  //进入房间之后调用才有效
	ReeChatMain::sharedInstance().StartSendVideo();
函数原型：
	int StartSendVideo();
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
返回值|int| 0 失败, 1 成功

- 显示本地视频：
```Objective-C
  //进入房间之后调用才有效
	ReeChatMain::sharedInstance().ObserverLocalVideoWindow(false);
函数原型：
  SdkErrorCode ObserverLocalVideoWindow(bool enable, void* ptrWindow = nullptr);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
enable|boolean|true:打开, false:关闭
view|SurfaceView|本地视频渲染窗口
返回值|int|0 失败, 1 成功


- 接收远端视频：
```Objective-C
  //进入房间之后调用才有效
	ReeChatMain::sharedInstance().ObserverRemoteTargetVideoV1("",ovideoView);
函数原型：
	int ObserverRemoteTargetVideoV1(const char* userID, void* ptrWindow);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
view|SurfaceView|远端视频渲染窗口
target|String| 传入的字符是用户名，用以查看指定用户的视频，空字符串代表查看混合视频
返回值|int|0 失败, 1 成功

- 关闭远端视频：
```Objective-C
	ReeChatMain::sharedInstance().ObserverRemoteTargetVideoV1("",null);
函数原型：
	int ObserverRemoteTargetVideoV1(String target,SurfaceView view);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
view|SurfaceView|远端视频渲染窗口
target|String| 传入的字符是用户名，用以查看指定用户的视频，空字符串代表不看远端视频
返回值|int|0 失败, 1 成功

- 切换发送视频源：
```Objective-C
主线程中调用，进入房间之后调用才有效
	ReeChatMain::sharedInstance().SwitchVideoSource(kVideoSourceBackCamera);
函数原型：
	int SwitchVideoSource(enVideoSource sourceIndex, void* ptrScreenView = nullptr);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
videoSource|int| 1:前置摄像头, 2:后置摄像头
返回值|int|0 失败, 1 成功


- 打开美颜：
```Objective-C
  //进入房间之后调用才有效
	ReeChatMain::sharedInstance().EnableBeautifyFilter(sender.isOn);
函数原型：
	void EnableBeautifyFilter(bool enabled);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
enabled|boolean| true:打开美颜；false:关闭美颜

- 变声：
```Objective-C
主线程中调用，进入房间之后调用才有效
	ReeChatMain::sharedInstance().SetVoiceChangeParam(pitch);
函数原型：
	int SetVoiceChangeParam(int pitch, int reverbLevel);
```
参数说明：
参数|类型|说明
:-:|:-:|:-:
pitch|int|pitch 取值范围为 [-10,10]
reverbLevel|int|取值范围为[0 ~ 9], 0 为原始声音，1到9，混响级别逐渐增强
