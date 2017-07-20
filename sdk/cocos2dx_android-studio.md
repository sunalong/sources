1.在app目录下创建文件夹libs，将下载的libs中的Android中的文件拷到libs目录下，并Add AS library

2.配置Android.mk
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
3.配置Application.mk:
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
4.在AndroidManifest.xml中添加权限：
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
5.在启动Activity中添加初始化：
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
