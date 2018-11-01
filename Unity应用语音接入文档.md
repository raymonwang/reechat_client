# Unity3D语音文档

## 下载SDK
> [下载Unity3D SDK](http://www.reechat.org)

## 双击xxx.unitypackage导入或则直接复制 Unity3D/ 下内容到 Assets/下


## 系统配置  
   - 在iOS ／android, Player setting 设置宏（RTCHAT_ENABLE）开关，否则无法调用语音功能。
   ![](https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/unity_macro.png)

   - 初始化接口，注册回调事件接口，只能执行一次，使用状态值判定 或 是放至单例脚本的Awake或Start函数中.
   示例
   ```
   public static bool isStart = false;
   void Start()
   {
       if (!isStart)
       {
           isStart = true;
           VoiceManager.OnPlayOverComplete += (int retCode) =>
                  {
                      Debug.Log(" startplayFinishResult  In C# Client re = " + retCode);
                  };
       }
   }
   ```
  - **接口需要主线程调用。**

  - **默认麦克风打开，扬声器关闭**

  - **按home键进入后台，如果想要没有声音需要 设置SpeakerVolum（0） 扬声器听筒都没有声音了，CloseMic()说话对方听不到**

## 基本API调用

- 初始化接口, 在其他接口之前调用
   ```c
   //初始化sdk(appid ,appkey)
    void InitUSDK (string appId, string appKey)

   ```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| appId      | string | 应用标识,可以联系我们申请 |
| appKey     | string      |   应用标识,可以联系我们申请|

示例代码*
```
  VoiceManager.Instance.InitUSDK (appid, appKey);
```


- 加入房间接口
  ```
  // 进入房间（roomid:房间id，cb加入房间回调）
  void JoinRoom (string roomId)；
  ```

| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| roomId      | string | 房间号 |
| retCode      | int |加入房间状态：1成功，0失败 |

示例代码*
```
VoiceManager.Instance.JoinRoom (roomidValue);
```


- 退出房间接口
  ```
  //退出房间（roomid:房间id，cb加入房间回调）
  void QuitRoom ()；
  ```


示例代码*
```
VoiceManager.Instance.QuitRoom ();
```


- 关闭扬声器

  ```
   //关闭扬声器外放功能
    void CloseLoudSpeaker()
  ```

示例代码*
```
VoiceManager.Instance.CloseLoudSpeaker ();
```


- 打开扬声器

```
//打开扬声器外放功能
void OpenLoudSpeaker ()
```

示例代码*
```
VoiceManager.Instance.OpenLoudSpeaker ();
```


- 打开麦克风
```
  //打开麦克风
  void OpenMic ()
```

示例代码*
```
VoiceManager.Instance.OpenMic ();
```


- 关闭麦克风
```
  //关闭麦克风
  void CloseMic();

```

示例代码*
```
VoiceManager.Instance.CloseMic ();
```


- 设置语音外放音量调节

```
   //设置语音外放音量调节（取值范围为0-10的整数，最大值请根据回音情况做调节）
   public void SpeakerVolum (float volume)；
```

   | 参数        | 类型           | 意义   |
   | ------------- |:-------------:| -----:|
   | volume     | float      |   音量调节，范围（0，10）|


示例代码*
```
VoiceManager.Instance.SpeakerVolum (4.0);
```


---
- 开始录制语音消息

```
  //开始录制语音消息（true 需要， false 不需要）
  void StartRecordVoice (bool needConvertWord)；
```

   | 参数        | 类型           | 意义   |
   | ------------- |:-------------:| -----:|
   | needConvertWord     | bool      |   是否需要翻译为文字 true 需要， false 不需要|

示例代码*
```
VoiceManager.Instance.StartRecordVoice (true);
```



-停止录制语音消息
   ```
   //停止录制语音消息（录制回调）
   void StopRecordVoice ()
   ```

示例代码*
```
VoiceManager.Instance.StopRecordVoice ();
```


- 播放录制语音消息

```
  //播放录制语音消息
  void StartPlayVoice (string voiceUrl)；
 ```
   | 参数        | 类型           | 意义   |
   | ------------- |:-------------:| -----:|
   | voiceUrl     | string      |   语音上传地址 （录音完成回调 取得）|


示例代码*
```
VoiceManager.Instance.StartPlayVoice ("http://xxxxxxx");
```


- 取消录制语音消息
```
  //取消录制语音消息
  void CancelRecordVoice ()；
```

示例代码*
```
VoiceManager.Instance.CancelRecordVoice ();
```


- 停止播放语音消息
```
  //停止播放语音消息
  void StopPlayVoice ()；
```

示例代码*
```
VoiceManager.Instance.StopPlayVoice ();
```

- 设置用户消息
```
   //停止播放语音消息
  public void SetUserInfo (string username, string userkey)；
```

  | 参数        | 类型           | 意义   |
  | ------------- |:-------------:| -----:|
  | username     | string      |   用户名|
  | userkey     | string      |   可为空|


示例代码*
```
VoiceManager.Instance.SetUserInfo ("username","");
```


- 加入房间回调
```
 //回调
 public delegate void JoinRoomCompleteHandler (int retCode);
```

| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| retCode      | int |加入房间状态：1成功，0失败 |

示例代码*
```
VoiceManager.OnJoinRoomComplete += (int retCode) =>
{
    Debug.Log(" joinroomResult  In C# Client re = " + retCode);
};
```


- 退出房间回调
```
 //回调
 public delegate void QuitRoomCompleteHandler (int retCode);
```

| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| retCode      | int |加入房间状态：1成功，0失败 |

示例代码*
```
VoiceManager.OnQuitRoomComplete += (int retCode) =>
        {
            Debug.Log(" quitroomResult  In C# Client re = " + retCode);
        };
```


- 播放完成回调
```
 //回调
 public delegate void PlayFinishCompleteHandler (int retCode);
```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| retCode      | int |加入房间状态：1成功，0失败 |

示例代码*
```
VoiceManager.OnPlayOverComplete += (int retCode) =>
       {
           Debug.Log(" startplayFinishResult  In C# Client re = " + retCode);
       };
```


- 录制完成回调
```
 //回调
 public delegate void VoiceRecordCompleteHandler (int retCode, string url, string text,float duration);
```

| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| retCode     | int      |   停止录制状态 1 成功， 0 失败|
| url     | string      |   语音上传地址|
| text     | string      |   翻译的文字|
| duration     | float      |   语音时间长度|

示例代码*
```
VoiceManager.OnVoiceRecordComplete += (int retCode, string url, string text, float duration) =>
       {
           Debug.Log(" voiceRecordResult  In C# Client re = " + retCode + ",url = " + url + ",duration = " + duration);
           if (url != null)
           {
               voiceRecordUrl = url;
           }
       };
```
