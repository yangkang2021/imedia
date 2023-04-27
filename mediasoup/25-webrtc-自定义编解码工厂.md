# webrtc 自定义编解码工厂
> 主要以视频为主，音频用webrtc内建

### (一)编码主要实现函数

```
    bool InitEncoder(int width, int height, int bitrate, CodecType codectype);
    void ReleaseEncoder(int error = 0);
    bool Encode(void *ydata, void *udata, void * vdata, 
                int ystride,int ustride, int vstride, 
                webrtc::EncodedImage *dst);

    void ForceIntraFrame(bool);
    void SetBitrate(int bitrate);
```
### (二)解码主要实现函数
```
    bool InitDecoder(CodecType codectype);
    void ReleaseDecoder(int error = 0);
    rtc::scoped_refptr<webrtc::I420Buffer> Decode(void *data, int size, uint64_t timestamp);
```

