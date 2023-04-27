# 基于webrtc的全平台-屏幕共享-方案

### 一. 屏幕共享需求：
1. 视频: pc端桌面或者窗口可以指定，移动端就桌面。
2. 音频: 支持系统声音共享，原有的mic声音保持通话。

### 二. webrtc自身的屏幕共享方案

1. pc：windows，mac，linux
   - 用各自平台api：实现了窗口和屏幕采集
   - 代码目录：webrtc\src\modules\desktop_capture
   - **分析：没有录制系统声音，mic的声音在通话流。**
   - [参考](https://blog.csdn.net/zhangpeng_linux/article/details/85858475)
2. android
    - 采用MediaProjection实现了屏幕采集
    - 代码目录：webrtc\src\sdk\android\api\org\webrtc\ScreenCapturerAndroid.java
    - **分析：没有录制系统声音，mic的声音在通话流。**
    - [MediaProjection使用参考](https://www.itxm.cn/post/23918.html)
3. ios
    - **没用实现**
4. web端
    - 用chrome自带的api

### 三. 我们的方案
1. pc(windows/mac/linux)：**用obs**。
    - mac屏幕共享需要用oc的代码申请权限
2. Android：**在webrtc android的基础上增加AudioTrack+AudioPlaybackCapture录制系统声音**。
    - AudioTrack + MediaRecorder.AudioSource.MIC/Default
    - AudioTrack + AudioPlaybackCapture：后者需要Android10及以后支持，而且需要前台service告诉用户正在录屏。
    - 需要自己处理横竖屏切换时重启录制画面，获取mediaProjectionPermission权限数据。
    - [AudioPlaybackCapture官方文档](https://developer.android.google.cn/guide/topics/media/playback-capture#%E7%94%A8%E6%B3%95)
    - [android录音三个方案](http://www.ckzixun.com/jishuzixun/13058.html)
    - [android录音情况](https://blog.csdn.net/icewst/article/details/105659032)
    - [这里实现了了在webrtc里面支持AudioPlaybackCapture](https://github.com/ant-media/WebRTCAndroidSDK/pull/1)
3. ios：**用ReplayKit**。
    - RPScreenRecorder只能本应用内录屏
    - RPSystemBroadcastPickerView+独立进程的扩展应用实现，需要跨进程传递采集的数据。
    - 实现一个BroadcastExtensionLauncher
    - [参考实现1：https://github.com/sunchengxiu/Socket_ReplyKit](https://www.jianshu.com/p/bfae0aa01dde)
    - [参考实现2：https://github.com/githupchenqiang/ScreenShare](https://xie.infoq.cn/article/35f6be94dfc7ba96d9f9c41b8)

4. web端：用chrome自带的api。

### 四. 怎么处理：麦克风音频流 + 系统声音流的并存
- 方案1：多路音频流独立，可能有回音消除问题吗
- 方案2：混音成一路到通话流。

### 五. webrtc的is_screencast参数
- 设置sources来源是截屏，这意味着会选择适合屏幕共享的编码设置。也许不是最合适的处理方式，例如，文本文档截屏和youtube视频截屏等不同的需求。
- 也就是说在设置为true后，会对屏幕共享的流使用差别与普通Video的处理方式；实际测试后如果设置该参数为true后，WebRTC不会再动态调整分辨率；设置为true后，WebRTC里就不会对视屏动态调整分辨流但是会降低帧率。
- 来自：[参考1](https://www.codeleading.com/article/49914164675/) 和 [参考2](https://blog.csdn.net/lym594887256/article/details/107363032) 和[参考3](https://blog.css8.cn/post/779836.html)
- [webrtc之Android视频质量提升：保帧率降分辨率](https://www.jianshu.com/p/987352dfb9f5)
- 关于ContentHint { kNone, kFluid, kDetailed, kText };