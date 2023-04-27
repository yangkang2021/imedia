# worker封装层的对象模型
>worker有nodejs、rust、go的封装层，这里以go为例分析封装层，go与nodejs的实现基本一致

### 对象的创建与销毁
1. worker退出方式：
    1. 发送worker.close指令--主动退出
    2. 收到系统信号
    3. 内部异常
    4. 主动发信号 
2. 对象退出
    1. gin run占据主线程，直到关闭才退出
3. worker退出
    1. 创建失败直接panic(err)
    2. worker.On("died" --> os.Exit(1)
4. 通过close事件层次删除对象    

### demo的主流程
1. demo：NewServer创建N个worker
2. demo：server.Run() --> gin.RunTLS开启服务。 关闭程序是退出
3. demo：ws连上：独立协程：runProtooWebSocketServer创建room(worker.CreateRouter)，peer(HandleProtooConnection)，transport。transport.run进入死循环
4. demo: 处理信令handleProtooRequest
    - getRouterRtpCapabilities
    - join
    - createWebRtcTransport/connectWebRtcTransport
    - produce/closeProducer/pauseProducer/resumeProducer
    - pauseConsumer/resumeConsumer/setConsumerPreferredLayers/setConsumerPriority/requestConsumerKeyFrame
    - //这里没有consume信令，服务端直接转发
    - produceData
    - changeDisplayName ...
### 一 protoo项目使用步骤
1. 用websocket.Conn创建transport
2. 用transport，peerData创建peer：Room::CreatePeer(peerId,peerData, transport)
3. 用Room接口管理所有人
4. 用Peer接口与单人通讯
5. 注意：应用层只需要管理peerData就行了。
6. 对象模型：
    - Room
        - peers
            - transport //websocket传输通道
            - sents //发送的消息列表，等待回复
            - data //peer的数据
```
type Room struct {
	IEventEmitter
	locker sync.Mutex
	logger logr.Logger
	closed bool
	peers  map[string]*Peer
}
type Peer struct {
	IEventEmitter
	logger    logr.Logger
	id        string
	transport Transport
	sents     sync.Map
	data      interface{}
	closed    uint32
	closeCh   chan struct{}
}
type Transport interface {
	eventemitter.IEventEmitter
	fmt.Stringer
	Send(data []byte) error
	Close()
	Closed() bool
	Run() error
}
type WebsocketTransport struct {
	eventemitter.IEventEmitter
	logger logr.Logger
	mu     sync.Mutex
	conn   *websocket.Conn
	closed uint32
}
```
### 二 mediasoup-go项目对象模型
1. 不依赖protoo项目
2. 仅仅是封装核心6组件：
    - worker，router，transport，producer，consumer，RtpObserver。
    - 还有WebRtcServer，dataProducer，dataConsumer。
3. Worker 存储所有自己创建的的对象
    - channel
    - payloadChannel
    - webRtcServers
    - routers
        - transports
            - producers //单个transport下的推流
            - consumers
            - dataProducers
            - dataConsumers
        - producers //所有transport下的推流
        - rtpObservers
        - dataProducers
        - mapRouterPipeTransports
4. RtpObserver用于rtp包的分析，用于音量和活跃用户风险。不会自动运行，需要自己添加需要监视的producer。
```
type Worker struct {
	IEventEmitter
	// Worker logger.
	logger logr.Logger
	// Worker process PID.
	pid int
	// Channel instance.
	channel *Channel
	// PayloadChannel instance.
	payloadChannel *PayloadChannel
	// Closed flag.
	closed uint32
	// Custom app data.
	appData interface{}
	// WebRtcServers map
	webRtcServers sync.Map
	// Routers map.
	routers sync.Map
	// child is the worker process
	child *exec.Cmd
	// diedErr indices worker process stopped unexpectly
	diedErr error
	// waitCh notify worker process stopped expectly or not
	waitCh chan error

	// Observer instance.
	observer IEventEmitter

	cgoWorker *CgoWorker
}
type Router struct {
	IEventEmitter
	logger                  logr.Logger
	internal                internalData
	data                    routerData
	channel                 *Channel
	payloadChannel          *PayloadChannel
	closed                  uint32
	appData                 interface{}
	transports              sync.Map
	producers               sync.Map
	rtpObservers            sync.Map
	dataProducers           sync.Map
	mapRouterPipeTransports sync.Map
	observer                IEventEmitter
}
```
### 三 demo项目
1. 有自己独立的room模型，对一个protoo的room
2. 没有peer，peer所有的对象通过PeerData绑定到protoo的peer上
3. 创建worker，根据信令操作worker内部对象
4. 对象模型
    - Server
        - mediasoupWorkers //这里存了子对象
        - rooms
            - protooRoom //连接到protoo项目
            - mediasoupRouter //这里面也存了子对象
            - audioLevelObserver //ActiveSpeakerObserver默认没有开启
```
type Server struct {
	logger                 zerolog.Logger
	locker                 sync.Mutex
	config                 Config
	rooms                  sync.Map
	mediasoupWorkers       []*mediasoup.Worker
	nextMediasoupWorkerIdx int
}
type Room struct {
	mediasoup.IEventEmitter
	logger             zerolog.Logger
	peerLockers        sync.Map
	config             Config
	roomId             string
	protooRoom         *protoo.Room
	mediasoupRouter    *mediasoup.Router
	audioLevelObserver mediasoup.IRtpObserver
	bot                *Bot
	networkThrottled   bool
	broadcasters       sync.Map
	closed             uint32
}

type PeerData struct {
	locker sync.Mutex
	// // Not joined after a custom protoo "join" request is later received.
	Joined           bool
	DisplayName      string
	Device           DeviceInfo
	RtpCapabilities  *mediasoup.RtpCapabilities
	SctpCapabilities *mediasoup.SctpCapabilities

	// // Have mediasoup related maps ready even before the Peer joins since we
	// // allow creating Transports before joining.
	transports    map[string]mediasoup.ITransport
	producers     map[string]*mediasoup.Producer
	consumers     map[string]*mediasoup.Consumer
	dataProducers map[string]*mediasoup.DataProducer
	dataConsumers map[string]*mediasoup.DataConsumer
}
type PeerInfo struct {
	Id              string                     `json:"id,omitempty"`
	DisplayName     string                     `json:"displayName,omitempty"`
	Device          DeviceInfo                 `json:"device,omitempty"`
	RtpCapabilities *mediasoup.RtpCapabilities `json:"rtpCapabilities,omitempty"`
	Data            *PeerData                  `json:"-,omitempty"`
}
```

### 四 worker57条控制指令
1. worker.close                              
2. worker.dump                               
3. worker.getResourceUsage                   
4. worker.updateSettings                     
5. worker.createWebRtcServer                 
6. worker.createRouter
 ---   
1. webRtcServer.close                        
2. webRtcServer.dump   
 ---                
1. router.close                              
2. router.dump                               
3. router.createWebRtcTransport              
4. router.createWebRtcTransportWithServer    
5. router.createPlainTransport               
6. router.createPipeTransport                
7. router.createDirectTransport              
8. router.createActiveSpeakerObserver        
9. router.createAudioLevelObserver 
 --- 
1. transport.close                           
2. transport.dump                            
3. transport.getStats                        
4. transport.connect                         
5. transport.setMaxIncomingBitrate           
6. transport.setMaxOutgoingBitrate           
7. transport.restartIce                      
8. transport.produce                         
9. transport.consume                         
10. transport.produceData                     
11. transport.consumeData                     
12. transport.enableTraceEvent 
 ---   
1. producer.close                            
2. producer.dump                             
3. producer.getStats                         
4. producer.pause                            
5. producer.resume                           
6. producer.enableTraceEvent 
 ---       
1. consumer.close                            
2. consumer.dump                             
3. consumer.getStats                         
4. consumer.pause                            
5. consumer.resume                           
6. consumer.setPreferredLayers               
7. consumer.setPriority                      
8. consumer.requestKeyFrame                  
9. consumer.enableTraceEvent
 ---   
1. dataProducer.close                        
2. dataProducer.dump                         
3. dataProducer.getStats 
 ---     
1. dataConsumer.close                        
2. dataConsumer.dump                         
3. dataConsumer.getStats                     
4. dataConsumer.getBufferedAmount            
5. dataConsumer.setBufferedAmountLowThreshold
 --- 
1. rtpObserver.close                         
2. rtpObserver.pause                         
3. rtpObserver.resume                        
4. rtpObserver.addProducer                   
5. rtpObserver.removeProducer
 --- 