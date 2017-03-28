## Android应用语音接入文档

平台Android Studio

#### 一：下载sdk并解压
###### 1. [下载SDK](http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_zip/RTChatSDK_android_release_2.4.zip)

---
#### 二：项目配置
###### 2.1.加入libs
	创建Android Studio项目：ASDemo，<p/>
	把解压的后获取的文件放到ASDemo/app/libs中，然后Add as Library,如图：
<br>
<img src="http://cdn.mztgame.ztgame.com.cn/gavoice_rtchat/resource_img/addlibrary.png" width="430">
<br/>
```xml
其中support v4包是供unit等使用的，Android studio推荐使用gradle配置：
app/build.gradle 的dependencies标签中：
compile 'com.android.support:appcompat-v7:25.3.0'
```

###### 2.2.配置so库的加载
	在app/build.gradle的android标签中配置：

###### 2.3.配置权限：
```xml
AndroidManifest.xml配置：
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_SETTINGS" />
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.READ_CONTACTS"/>
```


***
#### 三：接入：
###### 3.1 注册，传入上下文context：
```java
主线程中调用
函数原型：
public void register(Activity activity);
```
参数说明：

参数|类型|意义
:-:|:-:|:-:
activity|android.app.Activity|当前的activity


###### 3.2 设置用户名与用户key:
```java
主线程中调用
函数原型：
    public native int setUserInfo(String username, String userkey);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
username|String|用户名
userkey|String|用户key


###### 3.3 初始化sdk:
```java
主线程中调用
函数原型：
    public void initSDK(String appId, String key);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
appId|String|官网上申请的appId
key|String|官网上申请的key



###### 3.4 进入房间：
```java
主线程中调用
函数原型：
    public native int requestJoinPlatformRoom(String roomId);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
roomId|String|房间名

###### 3.5 打开/关闭扬声器：
```java
主线程中调用，进入房间之后调用才有效
函数原型：
    public native int setLouderSpeaker(boolean enable);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
enable|boolean| true:打开扬声器；false:关闭扬声器

###### 3.6 调用音量：
```java
主线程中调用，进入房间之后调用才有效
函数原型：
    public native int adjustSpeakerVolume(float volumeValue);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
	volumeValue|float|音量数值，范围0-10


###### 3.7 设置录音参数：
```java
主线程中调用
函数原型：
    public native void setParams(String voiceUploadUrl, String xfId);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
voiceUploadUrl|String|语音文件服务器地址，上传录音文件时需要
xfId|String|讯飞ID,录音时将录音文件翻译成文字时需要，在讯飞官网上申请


###### 3.8 录音
```java
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
函数原型：
    public native boolean startRecordVoice(boolean needConvertWord);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
needConvertWord|boolean|是否需要将录音翻译成文字，true:翻译成文字



###### 3.9 停止录音
```java
主线程中调用，进入房间之后无法调用，必须离开房间或未进入房间
函数原型：
  public native boolean stopRecordVoice();
```


###### 3.10 取消录音：
```java
主线程中调用
函数原型：
    public native boolean cancelRecordedVoice();
```
###### 3.11 播放录音
```java
主线程中调用
函数原型：
    public native boolean startPlayLocalVoice(String filepath);
```
参数说明：

参数|类型|说明
:-:|:-:|:-:
filePath|String|录音完后自动上传回调返回来的录音文件地址url


###### 3.12 停止播放录音
```java
主线程中调用
函数原型：
public native boolean stopRecordVoice();
```

###### 3.13 反注册
```java
主线程中调用，当不使用本sdk时调用，一般在onDestroy中调用，
函数原型：
public native void unRegister();
```
---
#### 四：回调：
###### 4.1.回调的必要性：
	只有当初始化完成后才能调用进入房间的接口;
	只有进入房间成功后，才能调用调节音量、打开/关闭扬声器;
	只有录音结束、上传完成后才能调用播放录音的接口.
###### 4.2.原型：
```java
public void rtchatsdkListener(int cmdType, final int error, String dataPtr, int dataSize)
```
###### 4.3.参数说明：
参数|类型|说明
:-:|:-:|:-:
cmdType|int|7:进入房间 25:录音结束 35:播放结束
error|int|1代表成功，0代表失败
dataPtr|String|返回的字符串数据，json格式
dataSize|int|返回的字符串数据大小
###### 4.4.示例：
```java
receiveDataFromC = new ReceiveDataFromC();
receiveDataFromC.setRtChatSDKVoiceListener(new RTChatSDKVoiceListener() {
    @Override
    public void rtchatsdkListener(int cmdType, final int error, String dataPtr, int dataSize) {
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
