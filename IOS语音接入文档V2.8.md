## IOS应用语音接入文档 V2.8

平台Xcode
```xml
SDK说明：
一：本SDK支持两种语音模式：
	1.实时语音
		两人都进入同一个房间，就可以像打电话那样聊天，支持多人进入同一个房间
  2.离线语音
		如同微信发语音那样，录制一段语音发送到服务器，服务器返回此语音在服务器的地址，
		然后用户可将此地址发送给其他用户达到语音交流的效果
二：本SDK支持视频聊天，可直播
三：其他：
  暂时不支持模拟器，需要真机来开发调试
  根据业务需求，可选择只接入语音功能，以减小应用的体积
```

#### 一：下载sdk并解压
###### 1. [下载SDK](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_zip/RTChatSDK_ios_release_2.8.2.zip)

---
#### 二：项目配置
###### 2.1.配置静态库：
```xml
1.导入全部静态库和thirdpard目录下的文件到xcode工程。
2.导入include文件夹下的头文件到xcode源码目录。
3.确认工程的Build Settings->Other Linker Flags下有-ObjC配置，如无请自行添加
注:请在Build Phases下检查是否有增加了如下静态链接库，如无请添加
```
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/addlibrary.png" width="430">

###### 2.2.配置权限：
```xml
  检查录音和使用摄像头权限，请在info.plist文件中添加如下两个键值，
  Privacy – Microphone Usage Description
  Privacy – Camera Usage Description
```

***
#### 三：接入：
###### 3.1 注册回调：

```Objective-C
主线程中调用
RTChatSDKMain::sharedInstance().registerMsgCallback(sdkCallBack);
	用于注册回调，需要在initSDK之前调用
	只有当初始化完成后才能调用进入房间的接口;
	只有进入房间成功后，才能调用调节音量、打开/关闭扬声器;
	只有录音结束、上传完成后才能调用播放录音的接口.

```

###### 3.2 初始化sdk:
```Objective-C
主线程中调用，不可重复初始化
  RTChatSDKMain::sharedInstance().initSDK(appid, appkey);
用于初始化SDK


函数原型：
  SdkErrorCode initSDK(const char* appid, const char* key);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
appId|String|官网上申请的appId
key|String|官网上申请的key

###### 3.3 设置用户信息:
```Objective-C
主线程中调用
  RTChatSDKMain::sharedInstance().setUserInfo(username, "32261be4ed6fd0d90976da1f7a85237d");
函数原型：
  SdkErrorCode setUserInfo(const char* username, const char* userkey);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
username|String|用户名
userkey|String|用户key

###### 3.4 进入房间：
```Objective-C
主线程中调用
  RTChatSDKMain::sharedInstance().requestJoinPlatformRoom(roomid, kVoiceOnly, 4);
函数原型：
  SdkErrorCode requestJoinPlatformRoom(const char* roomid_p, enMediaType meida_type = kVoiceOnly, int layout_mode = 4);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
roomId|String|房间名
media_type|int|媒体类型，语音值为1，具体值见以下说明
layout_mode|int|为视频接口使用，用来确定可同时查看多少人的视频，语音随意填值
```xml
media_type的值及含义：
	kVoiceOnly = 1;//语音
	kVideo_normalDefinition = 3;//视频，普通画质
	kVideo_highDefinition = 7;//视频，高画质
	kVideo_veryHighDefinition = 11;//视频，较高画质
	kLookLiveBC = 16;//直播
```

###### 3.5 打开/关闭扬声器：
```Objective-C
主线程中调用，进入房间之后调用才有效
  RTChatSDKMain::sharedInstance().setLoudSpeaker(enable);
函数原型：
  SdkErrorCode setLoudSpeaker(bool enable);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
enable|boolean| true:打开扬声器；false:关闭扬声器

###### 3.6 调节音量：
```Objective-C
主线程中调用，进入房间之后调用才有效
  RTChatSDKMain::sharedInstance().adjustSpeakerVolume(value);
函数原型：
  SdkErrorCode adjustSpeakerVolume(float volume);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
	volumeValue|float|音量数值，范围0-10

###### 3.7 离开房间：
```Objective-C
主线程中调用
	RTChatSDKMain::sharedInstance().requestLeavePlatformRoom();
函数原型：
	SdkErrorCode requestLeavePlatformRoom();
```

###### 3.8 设置录音参数：
```Objective-C
主线程中调用
    RTChatSDKMain::sharedInstance().setParams("http://giant.audio.mztgame.com/wangpan.php", xfId);
函数原型：
    void setParams(const char* voiceUploadUrl, const char* xunfeiAppID);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
voiceUploadUrl|String|语音文件服务器地址，上传录音文件时需要
xfId|String|讯飞ID,录音时将录音文件翻译成文字时需要，在讯飞官网上申请


###### 3.9 录音
```Objective-C
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
  RTChatSDKMain::sharedInstance().startRecordVoice(true);
函数原型：
  SdkErrorCode startRecordVoice(bool needConvertWord = false);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
needConvertWord|boolean|是否需要将录音翻译成文字，true:翻译成文字



###### 3.10 停止录音
```Objective-C
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
  RTChatSDKMain::sharedInstance().stopRecordVoice();
函数原型：
  SdkErrorCode stopRecordVoice();
```


###### 3.11 取消录音：
```Objective-C
主线程中调用
  RTChatSDKMain::sharedInstance().cancelRecordedVoice();
函数原型：
  SdkErrorCode cancelRecordedVoice();
```
###### 3.12 播放录音
```Objective-C
主线程中调用
  RTChatSDKMain::sharedInstance().startPlayLocalVoice([[dicParam valueForKey:@"url"] UTF8String]);
函数原型：
  SdkErrorCode startPlayLocalVoice(const char* voiceUrl);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
filePath|String|录音完后自动上传回调返回来的录音文件地址url


###### 3.13 停止播放录音
```Objective-C
主线程中调用
  RTChatSDKMain::sharedInstance().stopPlayLocalVoice();
函数原型：
	SdkErrorCode stopPlayLocalVoice();
```

---

### 四：视频接入
###### 4.1 注册回调
  同以上语音
###### 4.2 初始化sdk:
	同以上语音
###### 4.3 设置用户名与用户key:
	同以上语音
###### 4.4 进入房间：
	同以上语音
###### 4.5 发送本地视频：
```Objective-C
主线程中调用，进入房间之后调用才有效
	RTChatSDKMain::sharedInstance().startSendVideo();
函数原型：
	SdkErrorCode startSendVideo();
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
返回值|int| 0 失败, 1 成功

###### 4.6 显示本地视频：
```Objective-C
主线程中调用，进入房间之后调用才有效
	RTChatSDKMain::sharedInstance().observerLocalVideoWindow(false);
函数原型：
  SdkErrorCode observerLocalVideoWindow(bool enable, void* ptrWindow = nullptr);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
enable|boolean|true:打开, false:关闭
view|SurfaceView|本地视频渲染窗口
返回值|int|0 失败, 1 成功

###### 4.7 接收远端视频：
```Objective-C
主线程中调用，进入房间之后调用才有效
	RTChatSDKMain::sharedInstance().startObserverRemoteVideo(ovideoView);
函数原型：
	SdkErrorCode startObserverRemoteVideo(void* ptrWindow = nullptr);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
view|SurfaceView|远端视频渲染窗口
enable|boolean| true:打开扬声器；false:关闭扬声器
返回值|int|0 失败, 1 成功

###### 4.8 关闭远端视频：
```Objective-C
主线程中调用
	RTChatSDKMain::sharedInstance().stopObserverRemoteVideo();
函数原型：
	SdkErrorCode stopObserverRemoteVideo();
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
返回值|int|0 失败, 1 成功

###### 4.9 切换发送视频源：
```Objective-C
主线程中调用，进入房间之后调用才有效
	RTChatSDKMain::sharedInstance().switchVideoSource(kVideoSourceBackCamera);
函数原型：
	SdkErrorCode switchVideoSource(enVideoSource sourceIndex, void* ptrScreenView = nullptr);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
videoSource|int| 1:前置摄像头, 2:后置摄像头
返回值|int|0 失败, 1 成功

###### 4.10 切换模式显示：
```Objective-C
主线程中调用，进入房间之后调用才有效
切换到单人模式观看，传入指定的用户名即可
若传入为null则为看会议视频
	RTChatSDKMain::sharedInstance().switchRemoteTarget(RTChatHelper::instance().currentUser().c_str());
函数原型：
	SdkErrorCode switchRemoteTarget(const char* userID);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
userId|char*| 被观看者id, 注:设为null则观看混合视频
返回值|int|0 失败, 1 成功


###### 4.12 打开美颜：
```Objective-C
主线程中调用，进入房间之后调用才有效
	RTChatSDKMain::sharedInstance().enableBeautify(sender.isOn);
函数原型：
	void enableBeautify(bool enabled);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
enabled|boolean| true:打开美颜；false:关闭美颜

###### 4.13 变声：
```Objective-C
主线程中调用，进入房间之后调用才有效
	RTChatSDKMain::sharedInstance().setVoiceChangeParm(pitch);
函数原型：
	SdkErrorCode setVoiceChangeParm(int pitch);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
pitch|int|pitch 取值范围为 [-10,10]
