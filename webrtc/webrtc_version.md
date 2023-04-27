## 1. 概述
- webrtc随着chromium一起发布,大改一个半月发布一个版本
- chromium发布计划：https://chromiumdash.appspot.com/schedule

## 2. 应用项目对webrtc的依赖版本：
1. Chrome 正式版本: 版本 107.0.5304.107（正式版本） （64 位） 2022-11-18
2. chrome beta下载地址：https://www.google.com/chrome/beta/
2. mediasoup：不区分版本，或者>=M84
3. libmediasoupclient：M94
4. srs-webrtc：
5. webrtc新官网: https://webrtc.org
6. webrtc老官网: https://webrtc.github.io/webrtc-org
7. webrtc官方发布记录：https://webrtc.googlesource.com/src/+/refs/heads/main/docs/release-notes.md

## 3. webrtc版本发布记录
### M107(2022年10月17日)
1. 公告(0)
2. 亮点功能(0)
3. 新功能(9)
    - Add support for the abs-capture-time header extension.
    - Add new parameter for H.264
    - Remove AsyncInvoker
    - webrtc-internals should give an indication of what sendEncodings were passed
    - Implement and ship DisplayMediaStreamConstraints.surfaceSwitching
    - Implement and ship DisplayMediaStreamConstraints.systemAudio
    - Region Capture Support crop tokens on all Element types
    - Share-this-tab-intead - Tracking Bug
    - Preferred Display-Surface - Tracking Bug
4. bug修复(22)
 
[查看M107更多信息](https://groups.google.com/g/discuss-webrtc/c/StVFkKuSRc8/m/aL629ZA6AgAJ)

### M106
没用发出版本通知更新

### M105(2022年8月18日)
[查看M105更多信息](https://groups.google.com/g/discuss-webrtc/c/5KBtZx2gvcQ/m/HYKhV8ERDgAJ)

### M104(2022年7月4日)
[查看M104更多信息](https://groups.google.com/g/discuss-webrtc/c/PZxgk-aUFhw/m/5CkxzxUMAgAJ)

### M100-M103
没用发出版本通知更新

### M99(2022年3月4日)
[查看M99更多信息](https://groups.google.com/g/discuss-webrtc/c/Yf6c3HW4N3k/m/3SC_Hy15BQAJ)

### M98(2022年2月14日 )
1. 公告
2. 亮点功能
    - 在 getDisplayMedia 的选项卡共享功能区添加“改为共享此选项卡”按钮
    - 区域采集 - 用于裁剪自捕获视频轨道的 API
3. 新功能和bug修复

[查看M98更多信息](https://groups.google.com/g/discuss-webrtc/c/uQKmoaL93kE/m/a5NyC3gnBwAJ)


### M97(2022年1月17日)

[查看M97更多信息](https://groups.google.com/g/discuss-webrtc/c/-M808zqlSRE/m/vMZ1q1N9AgAJ)

### M96 (2021年11月15日)
1. 公告
    - Plan B弃用
    - 用于 SCTP 传输的 DcSCTP 库的推出在 M96 中恢复
2. 亮点功能
    - 支持RFC 2198 冗余
    - 目前已经支持使用 RED 的 OPUS 音频冗余
3. 新功能和bug修复
    - 新功能：添加对 abs-capture-time 头部扩展的支持。
    - 新功能：dcSCTP
    - bug修复：TODO

[查看M96更多信息](https://groups.google.com/g/discuss-webrtc/c/Bp8OzBzipSc/m/0AC4OGhdAgAJ)

### M95 (2021年10月15日)
1. 公告
2. 亮点功能
    - 用于 SCTP 传输的 DcSCTP 库的推出开启了这一里程碑
3. 新功能和bug修复
    - 新功能：Chromium/WebRTC 使用的 Pipewire 版本从 0.2 升级到 0.3
    - 新功能：删除 3DES 密码套件
    - bug修复：TODO

[查看M95更多信息](https://groups.google.com/g/discuss-webrtc/c/SfzpFc-dH-E/m/JHlMpLO1AAAJ)

### M94 (4606, 2021年9月24日) 
commit 6584528aeb0f0e2ab4d14114aefeee7e5997ade9
1. 公告
    - MediaTrack 可插入流
    - 非标准的 RTCConfiguration.offerExtmapAllowMixed 选项已从 Chrome 中移除
2. 亮点功能
    - TODO
3. 新功能和bug修复
    - 新功能：为开发者构建默认启用 DCHECKS
    - bug修复：TODO

[查看M94更多信息](https://groups.google.com/g/discuss-webrtc/c/tFyWdqW2sQM/m/ebfZvC9VAgAJ)

### M93 (2021年8月27日)
1. 公告
    - Chromium 将禁止锁屏之后的摄像头采集
    - 如果协商了 MID 和 BUNDLE ，按负载类型解复用功能将被禁用。
2. 亮点功能
3. 新功能和bug修复
    - 新功能：在 Linux Wayland 会话中共享屏幕时丢失鼠标光标
    - 为 video_replay 添加 --start_timestamp 和 --stop_timestamp 参数
    - dcSCTP 库
    - 为 WebRTC 代理配备 Chrome 跟踪入口点
    - 为 dcSCTP 库实施循环调度程序
    - 支持 dcSCTP 库中的 bufferedAmountLowThreshold
    - 允许编码器指定分辨率对齐属性
    - dcSCTP 在只重置一路流时重置所有流
    - 为音频生成 RTCP 时使用编解码器速率
    - 使内部软件视频编解码器可注入和可选
    - bug修复：TODO

[查看M93更多信息](https://groups.google.com/g/discuss-webrtc/c/ws0_MYHIBOw/m/HZGn07uIAwAJ)

### M92 (2021年7月7日)
1. 公告
2. 亮点功能
3. 新功能和bug修复
    - 新功能：Unified Plan不支持两个BUNDLE
    - 将--start_timestamp和--stop_timestamp添加到 video_replay
    - 为API添加概念文档
    - 为PC级测试框架添加概念文档
    - bug修复：TODO
4. 弃用说明
    - 弃用和删除 RTP 数据通道

[查看M92更多信息](https://groups.google.com/g/discuss-webrtc/c/hks5zneZJbo/m/Z-p4AfCrCQAJ)

### M91 (2021年5月14日)
1. 公告
    - ==RTP数据通道已从Chrome和C++ API中移除。==
2. 功能变更
3. bug修复(31)
    - [macOS Capture]在采集器内部实现：视频帧降采样的硬件加速。然后给webrtc发送。
    - 为 webrtc::VideoFrame::id 传播添加新的 RTP 标头扩展。
    - 基于rtp扩展头，添加一个新的视频分析注入器。

[查看M91更多信息](https://groups.google.com/g/discuss-webrtc/c/ScUMkkGA9tw/m/twkshm1pAgAJ)

### M90 (2021年4月7日)
1. 公告
   - Plan B SDP已被弃用，将来会被彻底删除
2. 功能变更
   - MediaStreamTrack Insertable Streams：一个MediaStream和WebCodecs API的扩展。
   - getCurrentBrowsingContextMedia：正在开发一个获取当前Tab内容的试验性API。
3. bug修复(29)

[查看M90更多信息](https://groups.google.com/g/discuss-webrtc/c/8VgEFxD_S80/m/C6e_utBTAAAJ)

### M89 (2021年2月10日)
1. 公告
   - WebRTC的Plan B SDP语义将被弃用和移除。因为使用Unified Plan SDP的cpu占用低协商快。
   - rtp有效载荷类型[35-65]的使用。[96-127]已经用尽。
   - sdp属性a=extmap-allow-mixed将被默认提供。
2. 功能废弃
   - 删除了RTPFragmentationHeader类。
3. bug修复(39)

[查看M89更多信息](https://groups.google.com/g/discuss-webrtc/c/Zrsn2hi8FV0/m/7Y4RLluHAQAJ?pli=1)

### M88 (2020年12月16日)
1. 公告
2. 功能新增
    - VP8，VP9支持NV12，无需转换成I420。
3. 功能废弃
    - RTP数据通道被移除，用基于SCTP的数据通道代替。
4. bug修复

[查看M88更多信息](https://groups.google.com/g/discuss-webrtc/c/A0FjOcTW2c0/m/wd1TNLqQCAAJ)

### M87 (2020年11月6日)
1. 公告
    - 提前警告：RTP数据通道被移除，用基于SCTP的数据通道代替。
2. 功能新增
    - 支持SDP完美协商.
    - Transceiver增加新方法stop。
3. 功能废弃
4. bug修复

[查看M87更多信息](https://groups.google.com/g/discuss-webrtc/c/6VmKkCjRK0k/m/YyOTQyQ5AAAJ)

### M86 (2020年9月16日)

1. 公告
    - transceiver.stop() 内部实现有变化。
    - 修复堆栈溢出攻击漏洞CVE-2020-6514。
2. 功能新增
    - 增加H264的sdp参数，fmtp=sps-pps-idr-in-keyframe，设置是否每个关键帧携带sps,pps。
3. 功能废弃
    - [ResourceAdaptation]删除了AdaptationListener
4. bug修复

[查看M86更多信息](https://groups.google.com/g/discuss-webrtc/c/pKCOpi9Llyc/m/QhZjyE02BgAJ)

### M85 (2020年7月17日)
1. 公告
2. 功能新增
3. 功能废弃
    - 移除frame-marking RTP扩展
    - 1
    - 2
    - 3
4. bug修复
    - 默认视频质量分析接口，允许增加多个peer。
    - Connection::Reset()
    - 增加HEVC编码名"H265X"到media/base/media_constants.h。

[查看M85更多信息](https://groups.google.com/g/discuss-webrtc/c/Qq3nsR2w2HU/m/tSNv8SUsAgAJ)

### M84 (2020年6月3日)
1. 公告
    - getStats接口获得联播下每个子流的信息，用SSRC区分；
2. 功能新增
3. 功能废弃
4. bug修复
    - VoIP纯语音引擎API，只包含音频编解码、NetEQ、RTP/RTCP处理，网络IO交给APP。

[查看M84更多信息](https://groups.google.com/g/discuss-webrtc/c/MRAV4jgHYV0/m/A5X253_ZAQAJ)
### M83 (2020年4月23日)
1. 公告
2. 功能新增
3. 功能废弃
4. bug修复

[查看M83更多信息](https://groups.google.com/g/discuss-webrtc/c/EieMDYtQ9sg/m/1kPpuKakCQAJ)
### M82
因为Chrome82取消发布，所以M82取消发布.
### M81 (2020年2月28日)
1. 公告
    - Chrome在没有获得权限的时候enumerateDevices不再暴露用户设备ID。
2. 功能新增
3. 功能废弃
4. bug修复

[查看M81更多信息](https://groups.google.com/g/discuss-webrtc/c/a5_zncyPc3Y/m/KQG8NlsVBgAJ)

### M80
### M79
### M78
### M77
### M76
### M75
### M74
### M73
### M72
### M71
### M70
### M69
### M68
### M67
### M66
### M65
### M64
### M63
### M62
### M61
### M60
### M59
### M58
### M57
### M56
### M55
### M54
### M53
### M52
### M51
### M50
### M49
### M48
### M47
### M46
### M45
### M44
### M43
### M42
### M41
### M40
### M39
### M38
### M37
### M36
### M35
### M34
### M33
### M32
### M31
### M30
### M29
### M28
### M27(2013-5-7)

### 参考资料：
- https://chromium.googlesource.com/external/webrtc/+/refs/heads/master/docs/release-notes.md
- https://blog.csdn.net/sonysuqin/article/details/112235095


