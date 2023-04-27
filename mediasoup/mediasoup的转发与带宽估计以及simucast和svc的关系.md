# mediasoup的分发与带宽估计以及simucast/svc的关系
>大小流(simucast/svc)对sfu很重要，看看mediasoup的实现。
>
>一句话：推流靠与sfu带宽估计调整大小流(simucast/svc)的码率分辨率帧率，拉流sfu靠与拉流端的带宽估计动态选择码流而不是码率。

### 一. mediasoup的分发与带宽估计以及simucast/svc的关系
1. 每一路流：mediasoup是1对N的分发。 
2. N个拉流客户端的网络状况各种各样，sfu不会因此而随意调整推流端参数。
3. 推流端用simucast/svc推送多路流。默认vp8： 3个simucast，每个simucat3个svc，一共是9路流。
4. 推流客户端和sfu有带宽估计，会在你设定的范围内动态调整推流码率。
5. sfu是转发给推流端，码率不能变，只能选择码流。
6. sfu根据和拉流客户端的带宽估计动态选择码流：带宽大的就看高清流，低的就看标清流。当然拉流端也可以手动选择。


### 二. 补充：mediasoup优点
1. 流控方便：暂停，恢复，打开，关闭 很容易。
2. 干掉与sfu的sdp传输与协商：实现了自己的一套简单RtpParameters传递与协商。不在需要编辑sdp。
3. 参数控制简单：分辨率、码率、帧率、采样率、声道数，起始码率，最小码率，最大码率，codec，simulcast，svc。
4. 非常重要：支持基于simulcast/svc的多路流，实现拉流端码率自适应，但切换的是流。
5. mediasoup-demo的webscoket信令就是一个通讯系统：可以做很多媒体以外的事情，如文本聊天、透传设备控制指令等。
6. 视频会议架构模式：整个实现基于视频会议的room。
