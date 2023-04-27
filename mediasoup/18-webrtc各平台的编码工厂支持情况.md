# webrtc各平台的编码工厂支持情况
> webrtc各端都支持vp8,vp9,h264,av1. 但是内部在GPU加速，细分类型，打包格式上面有区别。

### 内建相关文件：
    ```
    //编码工厂
    #include "api/video_codecs/video_decoder_factory.h"          //api定义
    #include "api/video_codecs/builtin_video_decoder_factory.h"  //api适配层
    #include "media/engine/internal_decoder_factory.h"           //真正的定义
    
    #include "api/video_codecs/video_encoder_factory.h"
    #include "api/video_codecs/builtin_video_encoder_factory.h"
    #include "media/engine/internal_encoder_factory.h"
    
    //具体编码器类
    #include "modules/video_coding/codecs/vp8/include/vp8.h"
    #include "modules/video_coding/codecs/vp9/include/vp9.h"
    #include "modules/video_coding/codecs/h264/include/h264.h"
    #include "modules/video_coding/codecs/av1/include/"
    ```



### SDP获取参数：
- pc在init的时候，sdp层调用channel_manager，然后调用mediaengine。
    
   ```
     MediaSessionDescriptionFactory::MediaSessionDescriptionFactory(...) {
  //音频，VoiceEgine::Init构造函数收集好了，直接去取
  channel_manager->GetSupportedAudioSendCodecs(&audio_send_codecs_); //包含了payload和编码信息
  channel_manager->GetSupportedAudioReceiveCodecs(&audio_recv_codecs_);
  channel_manager->GetSupportedAudioRtpHeaderExtensions(&audio_rtp_extensions_);
  
  //视频，MediaSessionDescriptionFactory现在才收集
  channel_manager->GetSupportedVideoSendCodecs(&video_send_codecs_);//顺序由编码工厂决定,由VideoEngine统一分配payload，96开始。
  channel_manager->GetSupportedVideoReceiveCodecs(&video_recv_codecs_);
  channel_manager->GetSupportedVideoRtpHeaderExtensions(&video_rtp_extensions_);
  channel_manager->GetSupportedDataCodecs(&rtp_data_codecs_);
  
  // 计算和合并
  ComputeAudioCodecsIntersectionAndUnion();
  ComputeVideoCodecsIntersectionAndUnion();
    }
    //以上获取的参数存放到sdp，然后再setsdp的时候，通过SetRecvParameters给到channel
    ```

- 继续调用WebRtcVideoEngine

    ```
    //channel_manager->GetSupportedVideoSendCodecs调用
    std::vector<VideoCodec> WebRtcVideoEngine::send_codecs() const {
      return GetPayloadTypesAndDefaultCodecs(encoder_factory_.get()); //分分配payload，96开始。
    }
    
    std::vector<VideoCodec> WebRtcVideoEngine::recv_codecs() const {
      return GetPayloadTypesAndDefaultCodecs(decoder_factory_.get());
    }
    ```

- 最终还是来自编码工厂，所以Android和ios使用自己的硬件编解工厂，可以不用编译webrtc内核里面的openh264和ffmpeg。
    
```
    std::vector<SdpVideoFormat> InternalEncoderFactory::SupportedFormats() {
      std::vector<SdpVideoFormat> supported_codecs;
      supported_codecs.push_back(SdpVideoFormat(cricket::kVp8CodecName));
      for (const webrtc::SdpVideoFormat& format : webrtc::SupportedVP9Codecs())
        supported_codecs.push_back(format);
      for (const webrtc::SdpVideoFormat& format : webrtc::SupportedH264Codecs())
        supported_codecs.push_back(format);
      if (kIsLibaomAv1EncoderSupported)
        supported_codecs.push_back(SdpVideoFormat(cricket::kAv1CodecName));
      return supported_codecs;
    }
    
    std::vector<SdpVideoFormat> InternalEncoderFactory::GetSupportedFormats()
        const {
      return SupportedFormats();
    }
  ```

### 内建的对象模型：windows，linux，mac
- api：定义了四个工厂的接口。提供创建内建编码工厂函数。已经工厂适配器类。
- 接口一般是GetSupportedEncoders/GetSupportedFormats + CreateAudioCodcer
- media/engine：定义了内建的视频编解码工厂。我做这里改了编码格式顺序。
- modules：真正的编码实现都在这下面。
- 具体类型
- 视频编码：api层定义BuiltinVideoEncoderFactory，封装了media层的InternalEncoderFactory。
- 视频解码：api层直接创建media层的InternalDecoderFactory。
- 音频编码：api层用模板创建AudioEncoderFactoryT，参数是多个module里面的具体编码类型。
- 音频解码：api层用模板创建AudioDecoderFactoryT，参数是多个module里面的具体编码类型。

### Android对象模型：全在java层
- 音频封装内建的模型：BuiltinAudioEncoderFactoryFactory + BuiltinAudioDecoderFactoryFactory
- 视频有四套：默认使用第四套，没有直接调用内建的。
- 第一套：SoftwareVideoEncoderFactory/SoftwareVideoDecoderFactory：封装了vp8和vp9，注意缺h264.
- 第二套：HardwareVideoEncoderFactory/HardwareVideoDecoderFactory：封装了mediacodec。具体编码器为HardwareVideoEncoder。
- 第三套:  DefaultVideoEncoderFactory/DefaultVideoDecoderFactory：组合了前两套，默认使用。
- 第四套：PlatformSoftwareVideoDecoderFactory是Android平台的软解码api，和HardwareVideoDecoderFactory一样也是继承自MediaCodecVideoDecoderFactory。
### IOS对象模型：todo
- 音频封装内建的模型：在sdk封装了一套，但这套可以通过BuiltinAudioEncoderFactoryFactory + BuiltinAudioDecoderFactoryFactory调用。
- 视频两套：默认使用第一套，没有直接调用内建的。
- 第一套：RTCDefaultVideoDecoderFactory/RTCDefaultVideoEncoderFactory。默认使用，vp8+vp9+av1软编解，h264硬编码.
- 第二套：RTCVideoDecoderFactoryH264/RTCVideoEncoderFactoryH264。只有h264.