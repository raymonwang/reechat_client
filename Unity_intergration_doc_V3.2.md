# Unity3d intergration document

## Develop Tool: Unity3d

## SDK description:
  - This SDK support two running modes：
    - real-time voice
	    Some participants entered one voice room. They can chat each other like with phone call.
    - offline voice
	    User can record his voice to a audio file with sdk, then sdk will store the file to server side and feedback a record url.
	    User can dispatch the record url to any users and they can playback the record url again.
	    
  - This SDK support video conference and broadcast the conference to internet with rtmp stream or hls stream;

  - Caution:
    This SDK can only compile for iphone device(no simulator)

## download sdk and decompress it
> [download sdk from site](http://www.reechat.org)

## double click reechatsdk_unity_release_xxx.unitypackage and import the content to Assets directory


## Configuration  
   - Add REECHAT_ENABLE macro at "Script Define Symbols" of "Player setting", or else you can not call reechat function.
   ![](https://raw.githubusercontent.com/raymonwang/TvShow/master/imgs/unity_macro.png)

## Intergration：
### All interface should be call in main thread!

- Set join room callback function
```
VoiceManager.OnJoinRoomComplete += (int retCode) =>
{
  //process
  Debug.Log(" joinroomResult  In C# Client re = " + retCode);
};
```

| Parameters    | Type          | Description   |
| ------------- |:-------------:| -----:|
| retCode      | int |join room result：1-success，0-failed |

- Set quit room callback function
```
VoiceManager.OnQuitRoomComplete += (int retCode) =>
{
  //process
  Debug.Log(" quitroomResult  In C# Client re = " + retCode);
};
```

- Initialize sdk, you can only call this interface once during program life cycle.
```c
   //init sdk(appid ,appkey)
  void InitUSDK (string appId, string appKey)
```
| Parameters    | type           | Description： |
| ------------- |:-------------:| -----:|
| appId      | string | appId get from website or webmaster |
| appKey     | string | appKey get from website or webmaster|

  Example:
  ```
  VoiceManager.Instance.InitUSDK ("3768c59536565afb", "df191ec457951c35b8796697c204382d0e12d4e8cb56f54df6a54394be74c5fe");
  ```

- Set user info, please ensure that each client have different username:
```Objective-C
  VoiceManager.Instance.SetUserInfoU ("Tom", "");
```

- Request join room：
```Objective-C
  VoiceManager.Instance.JoinRoom (roomidValue);
```

- Request quit room ：
```
  VoiceManager.Instance.QuitRoom ();
```


- Close mobile speaker：
```
VoiceManager.Instance.CloseLoudSpeaker ();
```

- Open mobile speaker：
```
VoiceManager.Instance.OpenLoudSpeaker ();
```

- Open mobile mic
```
VoiceManager.Instance.OpenMic ();
```

- Close mobile mic
```
VoiceManager.Instance.CloseMic ();
```

- Adjust the output volume of speaker
```
VoiceManager.Instance.SpeakerVolum (4.0);
```

   | Parameters    | Type        | Description   |
   | ------------- |:-------------:| -----: |
   | volume     | float      |value can choosed between 0 to 10|

---
---
- Begin record IM message
```
VoiceManager.Instance.StartRecordVoice();
```

- Stop record IM message
```
VoiceManager.Instance.StopRecordVoice ();
```

- 取消录制语音消息
```
VoiceManager.Instance.CancelRecordVoice ();
```

- Start Play the recorded url
```
VoiceManager.Instance.StartPlayVoice ("http://xxxxxxx");
```
   | Parameters    | Type           | Description   |
   | ------------- |:-------------:| -----:|
   | voiceUrl     | string      | recorded url from anther user|

- Stop Play the recorded url
```
VoiceManager.Instance.StopPlayVoice ();
```

| Parameters    | Type          | Description   |
| ------------- |:-------------:| -----:|
| retCode      | int |quit room result：1-success，0-failed |

- Set play voice finished callback function
```
VoiceManager.OnPlayOverComplete += (int retCode) =>
{
  //process
  Debug.Log(" startplayFinishResult  In C# Client re = " + retCode);
};
```
| Parameters        | Type      | Description   |
| ------------- |:-------------:| -----:|
| retCode      | int |play finshed result：1-success，0-failed |

- Set record voice finished callback function
```
VoiceManager.OnVoiceRecordComplete += (int retCode, string url, string text, float duration) =>
{
  //process
  Debug.Log(" voiceRecordResult  In C# Client re = " + retCode + ",url = " + url + ",duration = " + duration);
  if (url != null)
  {
    voiceRecordUrl = url;
  }
};
```

| Parameters        | Type           | Description   |
| ------------- |:-------------:| :-----|
| retCode     | int      |  record finshed result 1-success，0-failed|
| url     | string      |  recorded url |
| text     | string      |  the correspond words of recorded url|
| duration     | float      |  the duration of recorded url|

