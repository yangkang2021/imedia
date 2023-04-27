# libmediasoupclient介绍
（后面内容把PeerConnection简写成PC）

## 封装方案
1. webrtc的使用逻辑：全局一个PCFactory对象，然后每路通话各一个PC对象。
2. webrtc的核心参数控制：
    - factory：全局PCFactory最为关键：音频采集播放，音视频编解码器，线程控制等。
    - config：各个PC分别控制参数RTCConfiguration。
3. 对比libmediasoupclient：
   - 每个SendTransport/RecvTransport对应一个PC对象。
   - 每次创建Transport都可以传入控制参数factory和config。
   - 不传入控制参数就自动创建默认的factory和config。一般都要控制。
   - 用户用track来放入数据和接收数据就行了，是yuv格式。
4. 所以libmediasoupclient的使用方式：
   - android：用webrtc的android-sdk(google-webrtc-1.0.xxxxx.aar)的java创建pcfactory给libmediasoupclient使用。
   - ios：用webrtc的ios-sdk(WebRTC.framework)的oc接口创建pcfactory给libmediasoupclient使用。
   - 桌面开发：C++直接自己创建pcfactory给libmediasoupclient使用。

5. 桌面开发扩展：
   - 底层统一websokcet实现protoo协议。
   - 增加设备管理模块：音视频采集和渲染模块。
   - 增加自定编解码器：cuda和intel sdk。
   - 增加图像预处理器：集成opencv和深度学习。
 
 
## libmediasoupclient c++ sdk api 非常简单：
1. mediasoupclient：全局初始化和清理。
2. Device：代表一个终端，对应一个router。
    - CreateSendTransport（PeerConnection::Options，sever transport id），内部会创建一个PC。Options可以传入pcfactory进而控制编码器、音频采集模块等。
    - CreateRecvTransport（sever transport id），内部会创建另一PC。
3. SendTransport
    - Produce(server produce id，track注入音视频) 创建音视频生产者。往pc里面里面加track后调整sdp。
    - ProduceData(server produce id）) 创建数据生产者。       直接调整sdp。
4. RecvTransport
    - Consume（server Consume id）创建音视频消费者。 调整sdp，用track接收数据。
    - ConsumeData(server Consume id）) 创建数据消费者。 直接调整sdp。
5. Producer/DataProducer
    - GetTrack：track可以放入数据
6. Consumer/DataConsumer
    - GetTrack：track可以得到数据
7. 总结：
   - 一个SendTransport或者一个RecvTransport对应了一个pc，是分开的。
   - 用户用track来放入数据和接收数据就行了，是yuv格式。然后pc的会调用encoderfactory编码，pc的创建参数可配置。
   - 创建生产者和消费者就是修改sdp，同时注入或得到对应的track。
   - track是标准接口，可以任意定义：yuv_track或者ffmpeg_track。
   - transport，produce，consumer都需要先调用信令在server worker先创建，然后与客户端创建的对应起来。
   - router不需要单独信令，websocket连接时根据url就创建了roturer。


## pc的libmediasoupclient的不足:
1. libmediasoupclient的库依赖webrtc.lib，需要自己去编译后加入编译。而且仅仅一个webrtc.lib是不够的。
2. libmediasoupclient的头文件又依赖了webrtc的头文件。，需要添加webrtc的头文件和一些宏。
3. libmediasoupclient的接口需要webrtc的部分类：如StreamTrack和Peerconnectionfacotry。
4. libmediasoupclient本身只提供静态库。
5. 总结：libmediasoupclient要发布的话：需要合并更多webrtc的库，提取webrtc头文件给用户，并且用户要会webrtc的一些api。
   

---
## 对libmediasoupclient扩展demo模块:
pc做native开发，发现webrtc自带的device module有很多bug，现在有三个方案：
1. 完善和优化webrtc的device module
2. 从chromium/media移植
3. 用obs实现一个device module。屏幕录像啥的都有
