# webrtc怎么支持双声道立体声

### 1. 在一些直播和录播场景，需要webrtc支持立体声，提升用户体验

> 发现问题是检查确保：采集音源, 推流端SDP, 拉流端SDP. 拉流渲染设备都支持多声道。

    ```
    let replaceThis = "fmtp:111 minptime=10; useinbandfec=1"
    let replaceWith = "fmtp:111 minptime=10; useinbandfec=1; stereo=1; sprop-stereo=1"
    let sdpDescriptionWithStereo = sdp.description.stringByReplacingOccurrencesOfString(replaceThis,withString: replaceWith)
    ```
### 2. 怎么录制双声道数据？
1. 手动合并两个话筒的数据？
2. os系统有api支持？
3. 要买专门的立体声设备？

应该只有电影或者歌曲值得做立体声吧。 那俩都有专业的软件制作好数据。