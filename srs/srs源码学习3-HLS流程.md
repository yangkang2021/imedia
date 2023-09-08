# srs源码学习3-HLS流程

## 一. 对象模型
1. 用SrsTsMessageCache缓存数据，用SrsTsContextWriter写入
- SrsHls* hls; //SrsOriginHub
  - SrsRtmpJitter* jitter;
  - SrsHlsController* controller;    //对流的ts的分片
    - SrsHlsMuxer* muxer;            //对流的ts的分片
      - SrsHlsSegment* current;      //写一个ts片
        - SrsTsContextWriter* tscw;
          - SrsTsContext* context;   //ts封包
            - SrsFileWriter* writer; //文件操作 
      - SrsFragmentWindow* segments;
    - SrsTsMessageCache* tsmc;      //缓存数据
      - SrsTsMessage* audio;
        - SrsSimpleStream* payload; //缓存视频数据
      - SrsTsMessage* video;
        - SrsSimpleStream* payload; //缓存视频数据
        
## 二. 调用流程
1. 对象调用模型
   - main->SrsHybridServer->SrsServerAdapter->SrsServer->SrsRtmpConn->SrsLiveSource          //流到source
   - SrsLiveSource->SrsOriginHub->SrsHls->SrsHlsController->SrsHlsMuxer->SrsHlsSegment       //流到HLS模块
   - SrsHlsSegment->SrsTsContextWriter->SrsTsContext(encode)                                 //流到TS模型
2. SrsHls：initialize/on_publish/on_unpublish/on_audio/on_video
3. SrsRtmpJitter纠正：jitter->correct
3. SrsHlsController：initialize/on_publish/on_unpublish/write_audio/write_video
4. SrsHlsMuxer：initialize/on_publish/on_unpublish/flush_audio/flush_video
5. SrsHlsSegment+SrsTsContextWriter：write_audio/write_video
6. SrsTsContext：encode/encode_pat_pmt/encode_pes

## 三. 控制
1. SrsHlsController::reap_segment()：开启新切片
   - muxer->segment_close()：关闭切片
   - muxer->segment_open()：开启新切片
   - muxer->flush_video(tsmc)
   - muxer->flush_audio(tsmc)







