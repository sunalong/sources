## cocos2dx语音文档

##### 1.下载SDK
1.[Cocos2d语音SDK](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_zip/Cocos2d_GiantVoice.zip)

##### 2.系统配置  
2.1 iOS系统配置

>对于iOS的Xcode工程，只要将include目录和libs/iOS目录添加到Xcode工程中并设置头文件引用位置即可

![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_ios_import.png)

Xcode 显示为
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_ios_2.png)

需要添加的库
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_ios_3.png)


2.2 Android 系统配置

2.2.1 Eclipse配置：
 >把libs/Android/下文件放到proj.android/libs目录下。然后include和libs/Android目录放到合适的目录，比如工程下面建一个GiantVoice目录：

![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_1.png)

![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_2.png)

在proj.anroid/jni的Android.mk中添加对库文件和头文件的引用：
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_3.png)
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_4.png)
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_5.png)

在proj.anroid/jni的Application.mk中配置APP_ABI
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_8.png)

在proj.android/AndroidManifest.xml添加如下权限即可按照Cocos的编译方式
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_6.png)

最后需要在Java中初始化，比如：
![](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/coco_android_7.png)

2.2.2 Android Studio配置：

&emsp;&emsp;在app目录下创建文件夹libs，将下载的libs中的Android中的文件拷到libs目录下，并Add AS library

&emsp;&emsp;配置Android.mk
```java
    在app/jni/Android.mk中添加对库文件和头文件的引用：
    include $(CLEAR_VARS)
    LOCAL_MODULE := librtchatsdk
    LOCAL_SRC_FILES := ../../../GiantVoice/libs/Android/$(TARGET_ARCH_ABI)/librtchatsdk.so
    include $(PREBUILT_SHARED_LIBRARY)


    include $(CLEAR_VARS)
    LOCAL_MODULE := libamr-codec
    LOCAL_SRC_FILES := ../../../GiantVoice/libs/Android/$(TARGET_ARCH_ABI)/libamr-codec.so
    include $(PREBUILT_SHARED_LIBRARY)

    include $(CLEAR_VARS)
    LOCAL_MODULE := libmsc
    LOCAL_SRC_FILES := ../../../GiantVoice/libs/Android/$(TARGET_ARCH_ABI)/libmsc.so
    include $(PREBUILT_SHARED_LIBRARY)


    include $(CLEAR_VARS)

    $(call import-add-path,$(LOCAL_PATH)/../../../cocos2d)
    $(call import-add-path,$(LOCAL_PATH)/../../../cocos2d/external)
    $(call import-add-path,$(LOCAL_PATH)/../../../cocos2d/cocos)
    $(call import-add-path,$(LOCAL_PATH)/../../../cocos2d/cocos/audio/include)

    LOCAL_MODULE := MyGame_shared

    LOCAL_MODULE_FILENAME := libMyGame


    LOCAL_SRC_FILES := main.cpp
    LOCAL_SRC_FILES += ../../../Classes/AppDelegate/AppDelegate.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/Common/MainScene.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/Common/SectionMgr.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/Common/SectionScene.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/ImVoice/ImVoice.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/TeamVoice/TeamVoice.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/Utils/utils.cpp
    LOCAL_SRC_FILES += ../../../Classes/Scenes/config_sections.cpp
    LOCAL_SRC_FILES += ../../../Classes/Utils/Button/Button.cpp
    LOCAL_SRC_FILES += ../../../Classes/Utils/Button/LineButton.cpp
    LOCAL_SRC_FILES += ../../../Classes/Utils/Menu/ScrollMenu.cpp
    LOCAL_SRC_FILES += ../../../Classes/Utils/TextBox/TextBox.cpp

    # _COCOS_HEADER_ANDROID_BEGIN
    LOCAL_C_INCLUDES := $(LOCAL_PATH)/../../../GiantVoice/include
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/AppDelegate
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Scenes
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Scenes/Common
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Scenes/ImVoice
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Scenes/TeamVoice
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Scenes/Utils
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Utils
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Utils/TextBox
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Utils/Menu
    LOCAL_C_INCLUDES += $(LOCAL_PATH)/../../../Classes/Utils/Button
    # _COCOS_HEADER_ANDROID_END

    LOCAL_STATIC_LIBRARIES := cocos2dx_static

    # _COCOS_LIB_ANDROID_BEGIN
    LOCAL_SHARED_LIBRARIES += librtchatsdk
    # _COCOS_LIB_ANDROID_END

    include $(BUILD_SHARED_LIBRARY)
```
&emsp;&emsp;配置Application.mk:
```java
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
```
&emsp;&emsp;在AndroidManifest.xml中添加权限：
```xml
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
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!-- 语音sdk权限结束 -->
```
&emsp;&emsp;在启动Activity中添加初始化：
```java
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




2.3 **接口需要主线程调用。**

##### 3.基本API

###### 3.1初始化接口


*1.接口说明  *

初始化SDK,需要调用5个接口。

*2.函数原型 *

```C++
  //注册消息回调(主线程)
  void registerMsgCallback(const MsgCallBackFunc& func);
  //sdk初始化，只能调用一次(主线程)
  SdkErrorCode initSDK(const char* appid, const char* appkey);
  // 设置自定义参数
  void setParams(const char* voiceUploadUrl, const char* xunfeiAppID);
  //改变语音聊天登录用户信息
  SdkErrorCode setUserInfo(const char* username, const char* userkey);
  // 设置平台连接地址
  void customRoomServerAddr(const char* roomServerAddr);

```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| appId      | const char* | 应用标识,可以联系我们申请 |
| appKey     | const char*      |   应用标识,可以联系我们申请|
| username     | const char*      |   用户标识(不能相同)|
| voiceUploadUrl | const char*     |  录音上传地址 |
| xunfeiAppID | const char*     |    录音ID(Android 和iOS 会不一样) |
| roomServerAddr | const char*     |  自定义实时语音地址 |
| func | const MsgCallBackFunc&     | 消息回调 |
| 返回值 | SdkErrorCode     |  成功时返回OPERATION_OK |
*3.示例代码*
```C++
RTChatSDKMain::sharedInstance().registerMsgCallback(UserCustomClass::sdkCallback);

    RTChatSDKMain::sharedInstance().initSDK("5a152aa4c5d95196",
                                            "cc26305a8d124c5c55867d686d8b4655659d4f090673b4d518df3a4f354c0faa");

    RTChatSDKMain::sharedInstance().setParams("http://giant.audio.mztgame.com/wangpan.php", "");
    RTChatSDKMain::sharedInstance().setUserInfo(username.c_str(), "");
    RTChatSDKMain::sharedInstance().customRoomServerAddr("room.audio.mztgame.com:8080");

    //回调接口
   void UserCustomClass::sdkCallback(SdkResponseCmd cmdType, SdkErrorCode error, const char* dataPtr, uint64_t dataSize){
        CCLOG("TeamVoice::sdkCallback=%d,error=%d,mgs=%s", (int)cmdType, int(error),dataPtr);
     }
```

###### 3.2加入房间接口


*1.接口说明  *

实时语音，加入房间

*2.函数原型 *

```
//进入房间（roomid:房间id）
SdkErrorCode requestJoinPlatformRoom(const char* roomid_p, enMediaType meida_type = kVoiceOnly);


```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| roomid_p      | const char* | 房间号 |
| 返回值      | SdkErrorCode |成功时返回OPERATION_OK |



*3.示例代码*
```
 RTChatSDKMain::sharedInstance().requestJoinPlatformRoom("300");

```
###### 3.3退出房间接口


*1.接口说明  *

实时语音，退出房间

*2.函数原型 *

```
// 向平台请求退出房间(主线程)
SdkErrorCode requestLeavePlatformRoom();

```


*3.示例代码*
```
RTChatSDKMain::sharedInstance().requestLeavePlatformRoom();


```

###### 3.4关闭/打开扬声器


*1.接口说明  *

实时语音，关闭/打开扬声器

*2.函数原型 *

```
//关闭扬声器外放功能
SdkErrorCode setLoudSpeaker(bool enable);

```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| enable      | bool| 关闭：false,打开：true |
| 返回值      | SdkErrorCode |成功时返回OPERATION_OK |

*3.示例代码*
```
RTChatSDKMain::sharedInstace().setLoudSpeaker (false);

```

###### 3.5 关闭／打开麦克风


*1.接口说明  *

实时语音，关闭／打开麦克风

*2.函数原型 *

```
//关闭／打开 麦克风
SdkErrorCode setSendVoice(bool isSend);

```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| enable      | bool| 关闭：false,打开：true |
| 返回值      | SdkErrorCode |成功时返回OPERATION_OK |


*3.示例代码*
```
RTChatSDKMain::sharedInstace().setSendVoice (true);

```

###### 3.6 设置语音外放音量调节


*1.接口说明  *

实时语音，设置语音波形外放（默认系统音量）

*2.函数原型 *

```
//设置语音外放音量调节（取值范围为0-10的整数，最大值请根据回音情况做调节）
SdkErrorCode adjustSpeakerVolume (float volume)；

```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| volume     | float      |   音量调节，范围（0，10）|
| 返回值      | SdkErrorCode |成功时返回OPERATION_OK |

*3.示例代码*
```
RTChatSDKMain::sharedInstace().adjustSpeakerVolume (4.0);

```
---
###### 3.7 开始录制语音消息


*1.接口说明  *

语音消息，开始录制语音消息

*2.函数原型 *

```
//开始录制语音消息（需要翻译 true 需要， false 不需要）
SdkErrorCode startRecordVoice(bool needConvertWord = false);

```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| needConvertWord     | bool      |   是否需要翻译为文字 true 需要， false 不需要|


*3.示例代码*
```
RTChatSDKMain::sharedInstace().startRecordVoice (true);

```
###### 3.8 停止录制语音消息


*1.接口说明  *

语音消息，停止录制语音消息

*2.函数原型 *

```
//停止录制语音消息（录制回调）
SdkErrorCode stopRecordVoice ()

```


*3.示例代码*
```
RTChatSDKMain::sharedInstace().stopRecordVoice ();

```
###### 3.9 播放录制语音消息


*1.接口说明  *

语音消息，播放录制语音消息

*2.函数原型 *

```
//播放录制语音消息
SdkErrorCode startPlayLocalVoice (const char* voiceUrl)；

```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| voiceUrl     | string      |   语音上传地址 （录音完成回调 取得）|


*3.示例代码*
```
RTChatSDKMain::sharedInstace().startPlayLocalVoice ("http://xxxxxxx");

```
###### 3.10 取消录制语音消息


*1.接口说明  *

语音消息，取消录制语音消息

*2.函数原型 *

```
//取消录制语音消息
SdkErrorCode cancelRecordedVoice ()；
```

*3.示例代码*
```
RTChatSDKMain::sharedInstace().cancelRecordedVoice();

```
###### 3.11 停止播放语音消息


*1.接口说明  *

语音消息，停止播放语音消息

*3.函数原型 *

```
//停止播放语音消息
void stopPlayLocalVoice ()；
```

*3.示例代码*
```
RTChatSDKMain::sharedInstace().stopPlayLocalVoice ();

```
###### 3.12 设置用户消息


*1.接口说明  *

语音消息，设置用户消息

*2.函数原型 *

```
//停止播放语音消息
  SdkErrorCode setUserInfo(const char* username, const char* userkey);
```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| username     | const char*      |   用户名|
| userkey     | const char*      |   可为空|


*3.示例代码*
```
RTChatSDKMain::sharedInstace().setUserInfo ("cccc_2233","");

```
###### 3.13 回调
*1.接口说明  *

sdk回调接口

*2.函数原型 *

```
enum SdkResponseCmd {
       enRequestEnterRoom = 7,//加入房间
       enRequestLeaveRoom = 16,//退出房间
       enRequestRec = 25,//录制回调
       enNotifyPlayOver = 35, // 通知播放停止
}
enum SdkErrorCode {
       OPERATION_FAILED = 0,   //通用失败消息
       OPERATION_OK = 1,   //通用成功消息
}
//回调
static void sdkCallback(SdkResponseCmd cmdType, SdkErrorCode error, const char* dataPtr, uint64_t dataSize);
```
| 参数        | 类型           | 意义   |
| ------------- |:-------------:| -----:|
| cmdType      | SdkResponseCmd |消息类型 |
| error      | SdkErrorCode |错误类型 1成功，0失败 |
| dataPtr      | const char* | 如果有数据，会以json返回 |

*3.示例代码*
```
void UserCustomClass::sdkCallback(SdkResponseCmd cmdType, SdkErrorCode error, const char* dataPtr, uint64_t dataSize){
    CCLOG("TeamVoice::sdkCallback=%d,error=%d,mgs=%s", (int)cmdType, int(error),dataPtr);
    switch (cmdType) {
        case SdkResponseCmd::enRequestEnterRoom://加入房间
            break;
          case SdkResponseCmd::enRequestLeaveRoom://离开房间
            break;
          case SdkResponseCmd::enRequestRec://录制回调数据
            break;
        case SdkResponseCmd::enNotifyPlayOver://播放完成
            break;
        default:
            break;
    }
}


```
