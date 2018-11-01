# Android app intergration document

## Develop Tool: Android Studio

## SDK description:
  - This SDK support two running modes：
    - real-time voice
	    Some participants entered one voice room. They can chat each other like with phone call.
    - offline voice
	    User can record his voice to a audio file with sdk, then sdk will store the file to server side and feedback a record url.
	    User can dispatch the record url to any users and they can playback the record url again.
	    
  - This SDK support video conference and broadcast the conference to internet with rtmp stream or hls stream;

## download sdk and decompress it
> [download sdk from site](http://www.reechat.org/)

---
##  Project Setting
- Inport libs
	Depress packets and import it into your android project, then Add as Library. It looks like the following picture:

    <img src="https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/android_addlibrary.png" width="430">

```xml
We recommend using gradle as the config frame for Android studio.
You can insert this line into dependencies section of app/build.gradle file.

compile 'com.android.support:appcompat-v7:25.3.0'
```

- Config the setting of loading .so libs

```xml
    Insert the content into android section of app/build.gradle file.

	sourceSets{
	  main{
	      jniLibs.srcDir("libs")
	  }
	}
```

- Config privileges：
```xml
Check these settings in AndroidManifest.xml：
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
## Intergration：：
### You should call the following interface in the main thread
- Register context to sdk
```java
	public void register(Activity activity);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
activity|android.app.Activity|current activity


- Init SDK:
```java
    public void InitSDK(String appId, String key);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
appId|String|appId get from website
key|String|appkey get from website

- Set user name and user key, key can be "" now:
```java
    public native int SetUserInfo(String username, String userkey);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
username|String|用户名
userkey|String|用户key

###### Set room info:
```java
     public native int SetRoomType(int media_type, int layout_mode);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
media_type|int|media type
layout_mode|int|the parameter is used to define video layout mode
```xml
    media_type can be combine with the following enumeration value：
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

- Request join room：
```java
    public native int RequestJoinRoom(String roomId);
```

- Open or close mobile speaker：
```java
    // The interface can only be called after joined room
    public native int UseLouderSpeaker(boolean enable);
```

- Adjust pcm volumn
```java
    // The interface can only be called after joined room
    public native int AdjustSpeakerVolume(float volumeValue);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
	volumeValue|float|value can choosed between 0 to 10

- Request quit room ：
```java
    // The interface can only be called after joined room
	public native int RequestQuitRoom();
```

***
## Instant message Module interface
- start record voice into a audio file
```java
    //Can only be called when being outside the conference room
    public native boolean StartIMRecord(boolean needConvertWord, boolean isPersis, float scaleLevel);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
needConvertWord|boolean|whether translate voice to word
isPersis|boolean|wheher Persistence the record file
scaleLevel|float|alter the scale factor value，default value is 1.0


- stop record voice into a audio file
```java
    // Can only be called when being outside the conference room
     public native boolean StopIMRecord();
```


- Cancel record voice into a audio file：
```java
    // Can only be called when being outside the conference room
    public native boolean cancelIMRecord();
```

- Start Play the record url
```java
    // Can only be called when being outside the conference room
    public native boolean StartPlayIMVoice(String filepath);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
filePath|String|the record url from anther user


- Stop Play the record url
```java
    // Can only be called when being outside the conference room
	public native boolean StopPlayIMVoice();
```

- Unregister sdk
```java
    // It can be put in OnDestroy() function
	public native void unRegister();
```
---
## Use SDK CallBack：
- The necessary of sdk callback：
    - Many interface is run in aysnc mode, you can receive op status from callback.
    - Many interface can be run normally just after joined room in conference mode. 

- The prototype of callback：
```java
	public void SdkListener(int cmdType, final int error, String dataPtr, int dataSize)
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
cmdType|int|7:join room id 25:im record id 35:record url play over id
error|int|1. success，0, failed
dataPtr|String|description text(json format)
dataSize|int|the length of description text

- Example of sdk callback
```java
receiveDataFromC = new ReceiveDataFromC();
receiveDataFromC.set_voice_listener(new SdkVoiceListener() {
    @Override
    public void SdkListener(int cmdType, final int error, String dataPtr, int dataSize) {
        switch (cmdType) {
            case 1://sdk initialize cb
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(MainActivity.this, "Init finished" + error, 0).show();
                    }
                });
                break;
            case 7://join room cb
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        Toast.makeText(MainActivity.this, "Join room result:" + error, 0).show();
                    }
                });
                break;
            case 25://im record cb
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
                voiceTextLocal = voiceText;
                etResult.setText("tanslate voice to text：" + voiceTextLocal);
                break;
            case 35://im play over cb
                Log.i(TAG, "-MainActivity-play over result:" + error + " " + dataSize);
                break;
        }
    }
});
```


---
## Interface about video conference

- Start send local video：
```java
    // The interface can only be called after joined room
	public native int StartSendVideo();
```


- Stop show local video of mobile camera：
```java
    // The interface can only be called after joined room
	mNVEngine.ObserverLocalVideoWindow(isChecked, glLocalSurfaceViewContainer.getSurfaceView());
prototype：
    public native int ObserverLocalVideoWindow(boolean enable, SurfaceView view);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
enable|boolean|true:open remote video, false:close remote video
view|SurfaceView|The render view created by CreateAVideoWindow interface


- Observe Remote conference video：
```java
    // The interface can only be called after joined room
	mNVEngine.ObserverRemoteTargetVideoV1("",glRemoteSurfaceViewContainer.getSurfaceView());
prototype：
	public native int ObserverRemoteTargetVideoV1(String target,SurfaceView view);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
target|String|design the specified user video stream, "" mean confenrence video stream
view|SurfaceView|The window for render video stream

- Stop observe Remote conference video：
```java
    // The interface can only be called after joined room
	mNVEngine.ObserverRemoteTargetVideoV1("",null);
prototype:
	public native int ObserverRemoteTargetVideoV1(String target,SurfaceView view);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
target|String|design the specified user video stream, "" mean confenrence video stream
view|SurfaceView|nullptr will notify server to stop send video stream 


- Switch sending stram source：
```java
    // The interface can only be called after joined room
	mNVEngine.SwitchVideoSource(enVideoSource.kVideoSourceBackCamera.ordinal());
prototype：
	public native int SwitchVideoSource(int videoSource);
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
videoSource|int|1:front camera, 2:back camera, 3:screen share window, 4:video stream of local file, like mp4, mkv

- Start screen share：
```java
    // The interface can only be called after joined room
	VideoEngineImpl.getInstance().startScreenCapture(this);
prototype：
	public boolean startScreenCapture(Activity context)
```


- Enable beauty Filter：
```java
    // The interface can only be called after joined room
	mNVEngine.EnableBeautifyFilter(isBeautify);
prototype：
	public void EnableBeautifyFilter(final boolean enabled)
```
Parameters Description：
parameters|type|description
:-:|:-:|:-:
enabled|boolean|true:Enable beauty filter；false:Disable beauty filter
