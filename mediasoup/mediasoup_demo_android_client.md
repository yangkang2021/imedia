# mediasoup-Android-demo：
- 基于ibmediasoupclient的jni版本+protoo client的java版本
### 基础知识，Android demo组件化：
1. android数据绑定 https://www.jianshu.com/p/e4c4a9aece40
2. Android recyclerview https://www.jianshu.com/p/b4bb52cdbeb7
3. Android组件化：https://blog.csdn.net/jzman/category_9862777.html
    -  DataBinding：DataBindingUtil.setContentView(this,R.layout.activity_main)
    - BindingAdapter：扩展DataBinding支持逻辑处理
    - Observable模板类ObservableField<T>或@Bindable：值变了通知界面自动更新
    - ViewModel+LiveData+ViewModelProvider ：具有生命周期意识的，可监听变化的，Model数据管理。
    - Lifecycle生命周期一行就获取回调：getLifecycle().addObserver(new LifeCycleListener());
### Android demo 看RoomActivity:
1. initViewModel:
    - 6大数据模型：RoomInfo，Me(Info,DeviceInfo)，Peers(Peer<consumers>)，Producers，Consumers，Notify
    - RoomStore存放了所有的6大数据：roomInfo，me，peers，producers，consumers，notify。
    - RoomOptions启动时候的房间参数。
    - 数据变化，用RoomStore统一通知。
        - roomInfo数据变化：
            - id和url初始化变化
            - activeSpeakerId变化
        - me的数据变化：
            - peerId，displayName，device初始化变化
            - CanSendMic和CanSendCam变化
            - ...
        - Peers：add/remove/DisplayName变化
        - Producers：添加/删除/暂停/恢复
        - Consumers：添加/删除/暂停/恢复/CurrentLayers/ConsumerScore
            - 添加删除要更新：Consumers和Peers的consumer。
    - 界面DataBinding：绑定到XxxProps及其子类。EdiasProps用工厂管理所有XxxProps相关类、mRoomStore，和用connect向mRoomStore注册。
    - RoomClient控制RoomStore完成界面的刷新。
    - 都是选取第一个视频track显示。
    - 界面模块化：
        - activty:全局顶层信息：状态，按钮，peer容器，me对象
        - me
        - peer：peer和peer_view
2. 创建RoomClient
    - 创建了一个worker线程
    - 创建了一个websocket线程
3. mRoomClient.join():
    - 创建WebSocketTransport和proto。用peerListener接收数据。
        - onOpen-->joinImpl
        - onFail：正在重连，之前连接成功过。
        - onDisconnected：正在重连，之前没有连接成功过。
        - onClose
        - onRequest
            - newConsumer-onNewConsumer
                - mRecvTransport.consume
                - 更新mConsumers和peers
            - newDataConsumer-onNewDataConsumer
    - joinImpl：-->enableMic/enableCam
        - createSendTransport：Listener：onProduce/onConnect/onConnectionStateChange
        - createRecvTransport：Listener：onConnect/onConnectionStateChange
        - mProtoo.syncRequest("join",...);
        - 显示列表
        - enableMic/disableMicImpl：发布音频
            - createAudioTrack
            - mSendTransport.produce
            - mMicProducer.close()/closeProducer：关闭音频
        - enableCam/disableCamImpl：发布视频
            - createVideoTrack
            - mSendTransport.produce
            - mCamProducer.close()/closeProducer： 关闭视频发布
4. 怎么控制音视频：
    - 全局开关全部视频：enableAudioOnly/disableAudioOnly-->pauseConsumer/resumeConsumer.
    - 全局开关声音静音：unmuteAudio/muteAudio-->pauseConsumer/resumeConsumer.
    - 开关我的mic：muteMic/unmuteMic-->pause/resume
    - 开关我的相机：disableCam/enableCam-->close/removeProducer
    - 切换我的相机：changeCam-->CameraVideoCapturer
5. 怎么渲染的
6. 线程模型：
    - websocket一个线程
    - roomclient一个线程
    - ui主线程一个线程
    - pcfactory三线程


### android或其他客户端信令交互：绿色是信令
1. websocket连接：wss://ip:端口/?roomId=1234&peerId=xiaoming：服务端会分配worker和创建好router。
2. 初始化创建：
    - getRouterRtpCapabilities
    - createWebRtcTransport--id/ice参数-->createSendTransport--onconnect-->connectWebRtcTransport
    - createWebRtcTransport--id/ice参数-->createRecvTransport--onconnect-->connectWebRtcTransport
3. 加入房间：join-->房间里面已经有人发-->newConsumer通知-->mRecvTransport.consume收数据。
4. 创建生产者：mSendTransport.produce(track)-->onProduce回调-->produce在服务端创建消费者后把获取的又给客户端，有什么用呢？
5. 有人进入房间：newConsumer通知--得到id-->mRecvTransport.consume收数据。