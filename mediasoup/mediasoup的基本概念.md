# mediasoup的基本概念

### 一. mediasoup的对象
1. 标准PeerConnection在哪里？
    - 客户端：pc =PeerConnection
    - 一个Transport代表一个pc。 官方的接口都是提供两个pc。 
    - 一个用于推流sendTransport：不管多少路推流，都是这个pc。 
    - 一个用于拉流recvTransport：不管多少路推流，都是这个pc。
    - 所有的producer共用一个pc，当然你分别创建也行，但是没必要，一个就用。
    - consumer用另一个pc。其他同理
2. producer和comsumer
    - 一个producer就是发送PC的一个webrtcSendStream
    - 一个comsumer就是接收PC的一个webrtcRecvStream。
    - producer和comsumer的创建/暂停/回复/关闭 全部由客户端控制。 以发信令的方式。
    - producer由推流端主动创建。consumer由信令通知去sever创建。 
    - 有人推流，server会通知其他人："有人创建了producer"，其他人就能去创建consumer拉流。
3.  媒体路由器Router
    - mediasoup media 核心就是: 媒体路由器Router。
    - producer就是网Router加入一个输入，consumer就加一个输出。
    - producer和consumer是1对多关系。 创建时通过id建立路由表。

### 二. mediasoup应用场景 
1. 视频会议 
    - 直接看官方demo，每个room绑定一个router。 
    - 所以一切的东西(transport，producer，consumer)都在这个router内部。
2. 普通直播
    - 可以全局一个router，这种模式srs跟那样一样。 
    - 退化成没有房间的直播服务器，进行分发。
    - 如果你知道对方的id，可以不需要信令就能拉流。
    - 也就是传统直播可能只有 1个router；但mediasoup可以支持任意多个用于隔离；也就可以模拟srs的vhost的功能。
3. 点播
    - 在sfu，根据producer创建producer读取sfu文件。
4. sfu录制
    - sfu直接写文件
    - sfu转rtmp/rtsp到录制文件服务器
5. 屏幕共享
    - 录制桌面或者窗口，用一路producer推到sfu分发