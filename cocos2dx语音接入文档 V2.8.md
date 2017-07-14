## cocos2dx 2.8语音文档

```xml
SDK说明：
一：本SDK支持两种语音模式：
	1.实时语音
		两人都进入同一个房间，就可以像打电话那样聊天，支持多人进入同一个房间
  2.离线语音
		如同微信发语音那样，录制一段语音发送到服务器，服务器返回此语音在服务器的地址，
		然后用户可将此地址发送给其他用户达到语音交流的效果
二：其他：
  暂时不支持模拟器，需要真机来开发调试
  根据业务需求，可选择只接入语音功能，以减小应用的体积
```

### 一：下载SDK
1.[Cocos2d语音SDK](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_zip/RTChatSDK_cocos_release_2.8.2.zip)

---

### 二：系统配置  
###### 2.1 iOS系统配置

>对于iOS的Xcode工程，只要将include目录和libs/iOS目录添加到Xcode工程中并设置头文件引用位置即可

<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_ios_import.png" width="430">

Xcode 显示为:

<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_ios_2.png" width="430">

需要添加的库:

<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_ios_3.png" width="430">

###### 2.2 Android 系统配置

####### 2.2.1 Eclipse配置：
 >把libs/Android/下文件放到proj.android/libs目录下。然后include和libs/Android目录放到合适的目录，比如工程下面建一个GiantVoice目录：

<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_1.png" width="430">

<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_2.png" width="430">

在proj.anroid/jni的Android.mk中添加对库文件和头文件的引用：
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_3.png" width="430">
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_4.png" width="430">
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_5.png" width="430">

在proj.anroid/jni的Application.mk中配置APP_ABI
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_8.png" width="430">

在proj.android/AndroidManifest.xml添加如下权限即可按照Cocos的编译方式
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_6.png" width="430">
最后需要在Java中初始化，比如：
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_7.png" width="430">

####### 2.2.2 Android Studio配置：
```xml
  在app目录下创建文件夹libs，将下载的libs中的Android中的文件拷到libs目录下，并Add AS library
  配置Android.mk
    在app/jni/Android.mk中添加对库文件和头文件的引用：
```
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/cocos_studio1.png" width="430">
<br/>
<br>
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/cocos_studio2.png" width="430">
<br/>

```java
配置 Application.mk:
    在app/jni/Application.mk中配置对ABI的支持：
    APP_STL := gnustl_static

    APP_CPPFLAGS := -frtti -DCC_ENABLE_CHIPMUNK_INTEGRATION=1 -std=c++11 -fsigned-char
    APP_LDFLAGS := -latomic

    APP_ABI := armeabi-v7a

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

### 三：基本API
###### 3.1 接口需要主线程调用

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
