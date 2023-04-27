# webrtc详细分析(七)：webrtc的统计模块

chrome://webrtc-internals/

![](.webrtc详细分析(七)：webrtc的统计模块_images/347bbe91.png)
![](.webrtc详细分析(七)：webrtc的统计模块_images/37fa06fe.png)
![](.webrtc详细分析(七)：webrtc的统计模块_images/140b2bfe.png)

[json格式1](pub.json)
[json格式2](sub.json)

1. webrtc标准文档
    - https://www.w3.org/TR/webrtc/
    - https://www.w3.org/TR/mediacapture-streams/
    - https://www.w3.org/TR/webrtc-stats/
    - 22种统计类型，对象命名规则：codec --> RTCCodecStats
    ```
    enum RTCStatsType {
    "codec",
    "inbound-rtp",
    "outbound-rtp",
    "remote-inbound-rtp",
    "remote-outbound-rtp",
    "media-source",
    "csrc",
    "peer-connection",
    "data-channel",
    "stream",
    "track",
    "transceiver",
    "sender",
    "receiver",
    "transport",
    "sctp-transport",
    "candidate-pair",
    "local-candidate",
    "remote-candidate",
    "certificate",
    "ice-server"
    };
   
   
   
    RTCPeerConnectionStats
    
    //流信息
    RTCMediaStreamStats //获取到track列表
    RTCMediaStreamTrackStats //获取track对应的source
    
    RTCAudioSourceStats：RTCMediaSourceStats //音频分贝值
    RTCVideoSourceStats：RTCMediaSourceStats //帧率分辨率等
    
    //传输信息
    RTCTransportStats
    //收发包，丢包信息
    RTCInboundRTPStreamStats：RTCReceivedRtpStreamStats：RTCRTPStreamStats
    RTCOutboundRTPStreamStats：RTCRTPStreamStats
    RTCRemoteInboundRtpStreamStats：RTCReceivedRtpStreamStats：RTCRTPStreamStats
    RTCRemoteOutboundRtpStreamStats：RTCSentRtpStreamStats：RTCRTPStreamStats
    
    //ice信息
    RTCCertificateStats
    RTCIceCandidatePairStats
    RTCLocalIceCandidateStats：RTCIceCandidateStats
    RTCRemoteIceCandidateStats：RTCIceCandidateStats
    
    //其他信息
    RTCCodecStats
    RTCDataChannelStats


    ```
1. 两套统计模块接口代码与使用
    ```
     // peer_connection_interface.h
    
      // 历史遗留的老版本的api
      virtual bool GetStats(StatsObserver* observer,
                            MediaStreamTrackInterface* track,  // Optional
                            StatsOutputLevel level) = 0;
      
      // 标准获取统计数API
      virtual void GetStats(RTCStatsCollectorCallback* callback) = 0;
      
      // 标准获取发送统计API
      virtual void GetStats(
          rtc::scoped_refptr<RtpSenderInterface> selector,
          rtc::scoped_refptr<RTCStatsCollectorCallback> callback) = 0;
      
      // 标准获取接收统计API
      virtual void GetStats(
          rtc::scoped_refptr<RtpReceiverInterface> selector,
          rtc::scoped_refptr<RTCStatsCollectorCallback> callback) = 0;
      
      // 清楚缓存的统计信息
      virtual void ClearStatsCache() {}
    ```

    - 两套实现：pc/stats_connector.cc或者pc/rtc_stats_collector.cc
    - pc/peer_connection.cc的GetStats调用pc/stats_connector.cc或者rtc_stats_collector.cc的GetStatsReport实现，调用太频繁直接返回缓存。
    - 结果通过api/stats/rtc_stats_collector_callback.h的回调函数返回。
    - 返回的是一个基于引用计数的RTCStatsReport对象。
2. 统计对象模型：三层结构
    - RTCStatsReport：(时间，RTCStats列表)，
    - RTCStats：每个RTCStats包含一个RTCStatsMember(id,时间，type,members)列表。
    - RTCStatsMember：本身也可以是列表。
3. 三层结构的代码，api和stats文件夹下面分布定义了三层结构的头文件和实现。
    - RTCStatsReport:  rtc_stats_report.h/rtc_stats_report.cc：
    - RTCStats+RTCStatsMember基类： rtc_stats.h/rtc_stats.cc
    - RTCStats+RTCStatsMember子类： rtcstats_objects.h/rtcstats_objects.cc
    - 其他：stats_types.h +cc:定义了类型
4. RTCStats实现例子
    ```
    //试用例子:定义一个foostats统计对象
    
    //rtcfoostats.h:
    class RTCFooStats : public RTCStats {
     public:
      WEBRTC_RTCSTATS_DECL(); //定义RTCStatsMember列表
    
      RTCFooStats(const std::string& id, int64_t timestamp_us);
    
      RTCStatsMember<int32_t> foo; //定义成员1
      RTCStatsMember<int32_t> bar; //定义成员2
    };
    
    //rtcfoostats.cc:
    WEBRTC_RTCSTATS_IMPL(RTCFooStats, RTCStats, "foo-stats" &foo, &bar);//把成员加入到列表
    
    RTCFooStats::RTCFooStats(const std::string& id, int64_t timestamp_us) //构造函数
     : RTCStats(id, timestamp_us),
       foo("foo"),
       bar("bar") {
    }
    //------------------使用------------------
    RTCFooStats foo("fooId", GetCurrentTime());
    foo.foo = 1;
    foo.bar = 2;
    //遍历成员
    for (const RTCStatsMemberInterface* member : foo.Members()) {
      printf("%s = %s\n", member->name(), member->ValueToString().c_str());
    
    ```
5. android的统计：两套实现
    - 第一套：废弃。StatsObserver.java+StatsReport.java。 全是string值。
    - 第二套：最新。RTCStats.java + RTCStatsReport.java + RTCStatsCollectorCallback.java
    - examples/androidapp：用第一套间隔1秒定时获取。peerconnection给到CallActivity的onPeerConnectionStatsReady给到HudFragment。
    - 最新的Android的统计日志结构：二维的map结构，member都搞成object类型
        ```
        public class RTCStatsReport {
          private final long timestampUs;
          private final Map<String, RTCStats> stats;
        }
        
        public class RTCStats {
          private final long timestampUs;
          private final String type;
          private final String id;
          private final Map<String, Object> members;
        }
        ```
6. ios的统计：也是两套
    - RTCPeerConnection+Stats.mm
    - RTCLegacyStatsReport+Private.h + RTCLegacyStatsReport.h + RTCLegacyStatsReport.mm
    - RTCStatisticsReport+Private.h + RTCStatisticsReport.h + RTCStatisticsReport.mm
    - examples/objc:间隔一秒用peerConnection statsForTrack获取给到ARDVideoCallViewController.m的didGetStats然后给到ARDStatsView.m函数
    - 最新的ios的统计日志结构：二维的NSDictionary结构
    ```
    @interface RTC_OBJC_TYPE (RTCStatisticsReport) : NSObject
    
    /** The timestamp of the report in microseconds since 1970-01-01T00:00:00Z. */
    @property(nonatomic, readonly) CFTimeInterval timestamp_us;
    
    /** RTCStatistics objects by id. */
    @property(nonatomic, readonly) NSDictionary<NSString *, RTC_OBJC_TYPE(RTCStatistics) *> *statistics;
 
    @end
    
    /*---------------------------------------------------------------------*/
    /** A part of a report (a subreport) covering a certain area. */
    RTC_OBJC_EXPORT
    @interface RTC_OBJC_TYPE (RTCStatistics) : NSObject
    
    /** The id of this subreport, e.g. "RTCMediaStreamTrack_receiver_2". */
    @property(nonatomic, readonly) NSString *id;
    
    /** The timestamp of the subreport in microseconds since 1970-01-01T00:00:00Z. */
    @property(nonatomic, readonly) CFTimeInterval timestamp_us;
    
    /** The type of the subreport, e.g. "track", "codec". */
    @property(nonatomic, readonly) NSString *type;
    
    /** The keys and values of the subreport, e.g. "totalFramesDuration = 5.551".
        The values are either NSNumbers or NSStrings or NSArrays encapsulating NSNumbers
        or NSStrings, or NSDictionary of NSString keys to NSNumber values. */
    @property(nonatomic, readonly) NSDictionary<NSString *, NSObject *> *values;
    
    @end
    ```
    
    
```
//1. 总体网络状态：从candidate-pair得到
	int rtt = 0;          //ms： 网络延迟毫秒
    int bwe_send = 0;     //KBps：估计的发送带宽，或者从CallState
    int bwe_recv = 0;     //KBps：估计的接收带宽，或者从CallState
	int bitrate_send = 0; //KBps：实际的发送速度
	int bitrate_recv = 0; //KBps：实际的接收速度
    
    bool connected = false; //连接状态
    
    //每一路流
    bool isaudio = true;
    bool send = true;
    float loss = 0.0f;         //丢包率，发送和接收都可以有
    float audio_level = 0.0f;  //
    float bitrate_ = 0.0f;
    float packets_ = 0;
```


