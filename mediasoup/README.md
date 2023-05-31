# mediasoup知识体系

### 零. 概述
1. [mediasoup的优缺点](mediasoup的优缺点.md)
1. [mediasoup媒体控制与协商机制](11-webrtc-parameters.md)
1. [mediasoup的基本概念](mediasoup的基本概念.md)

### 一. mediasoup编译运行调试
1. [mediasoup的开源项目整理](01-mediasoup-opensource.md)
2. [mediasoup和mediasoup-demo编译](mediasoup_build.md)
3. [mediasoup的c++调试(vs2019/xcode/gdb)](mediasoup_cpp_debug.md)
4. [mediasoup-demo的docker部署-TODO](mediasoup_docker.md)

### 二. mediasoup基础
1. [mediasoup-demo的sever和app学习](mediasoup-demo)
    1. [前端app的架构](mediasoup-demo/1-前端app的架构.md)
    2. [前端app的gulp编译](mediasoup-demo/2-前端app的gulp编译.md)
    3. [mediasoup的bot](mediasoup-demo/mediasoup的bot.md)
    4. [media-demo-server源码入门](mediasoup-demo/demo-server-nodejs.md)
    4. [demo-server-nodejs源码分析](mediasoup-demo/demo-server-nodejs源码分析.md)
1. [mediasoup的web端demo的参数](13-mediasoup的web端demo的参数.md)
1. [mediasoup-demo信令](mediasoup_demo_signalling.md)
2. [libmediasoupclient介绍](libmediasoupclient_intro.md)
3. [mediasoup-demo的android客户端代码介绍](mediasoup_demo_android_client.md)
4. [protoo使用与实现](protoo.md)
5. [mediasoup的demo的websocket的keepalive与断网检测](mediasoup_websocket_pingpong.md)
6. [boardcaster与ffmpeg/gstreamer接入](boardcaster.md)
7. [mediasoup-录制](19-mediasoup-录制.md)
8. [mediasoup客户端创建producer流程](mediasoup客户端创建producer流程.md)

### 三. mediasoup编解码器
2. [webrtc各平台的编码工厂支持情况](18-webrtc各平台的编码工厂支持情况.md)
3. [webrtc-自定义编解码工厂](25-webrtc-自定义编解码工厂.md)
4. [mediasoup全端支持h264以及硬编硬解的方案](15-mediasoup全端支持h264以及硬编硬解的方案.md)
5. [mediasoup全端支持h265的4K的方案-未开始](16-mediasoup全端支持h265的4K的方案.md)
6. [自定义编解码器实现mediasoup-webrtc压力测试](26-mediasoup-webrtc压力测试.md)

### 四. mediasoup扩展
1. [webrtc和mediasoup的nack,fec,red](12-nack-fec-red.md)
2. [webrtc怎么支持双声道与立体声](14-mediasoup怎么支持双声道立体声.md)
3. [视频会议中优化sfu以吸取mcu的优点](21-mediasoup_sfu_vs_mcu.md)
4. [24-webrtc-视频会议中-集成人工智能与Pipeline](24-webrtc-集成人工智能.md)
5. [27-webassembly技术](27-webassembly技术.md)
6. [卡顿率统计.md](卡顿率统计.md)
7. [丢包率和重复发包次数的关系](丢包率和重复发包次数的关系.md)
8. [mediasoup的缓存](mediasoup的缓存.md)
9. [mediasoup的转发与带宽估计以及simucast和svc的关系](mediasoup的转发与带宽估计以及simucast和svc的关系.md)
11. [mediasoup弱网优化](mediasoup弱网优化.md)

### 四. mediasoup-worker-sdk的源码分析
1. [worker封装层的对象模型-以go为例)](23-mediasoup-worker封装层的对象模型.md)
2. [worker封装层的对象模型-以nodejs为例)](mediasoup-worker封装层分析-nodejs.md)
3. [mediasoup的rust接口与worker的启动方式](mediasoup-rust.md)

### 五. mediasoup-worker C++源码分析
1. [mediasoup-workder的cpp文件列表](worker-src-files.md)
2. [worker启动方式与启动参数](22-mediasoup-worker的启动参数.md)

### 六. mediasoup版本变化
1. [mediasoup的版本变更记录](https://github.com/versatica/mediasoup/blob/v3/CHANGELOG.md)
1. 2022-06-22版本3.10.0：支持单端口: [mediasoup官方单端口实现](mediasoup_singleport.md)
2. 2022-08-30版本3.10.6：与worker通讯协议变化: [mediasoup新版本的管道协议变化](mediasoup新版本的管道协议变化.md)
10. [mediasoup-v4新功能预测-分发有限路数的流并动态切换](mediasoup-v4新功能预测-分发有限路数的流并动态切换.md)

## 七. 项目研究
1. [libwebrtc项目](https://github.com/yangkang2021/libwebrtc)
2. [Mediasoup Server集群](https://github.com/yangkang2021/mediasoup_server_cluster)
3. [Mediasoup全平台客户端](https://github.com/yangkang2021/mediasoup_client_full_platform)