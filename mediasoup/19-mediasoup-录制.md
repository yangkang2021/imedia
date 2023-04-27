# mediasoup的sfu录制的一种方案

### 分析开源项目的录制原理
> https://github.com/ethand91/mediasoup3-record-demo.git
总结：
- 客户端信令控制开始和结束录制。按peer为单位。
- 每个人每个流各一个录制，按音视频分开，按人分开。
- 录制方法是用 plainRtpTransport，把流发给一个127.0.0.1udp端口，然后ffmpeg命令行或者gstream就收处理。
- plainRtpTransport录制的优点：在nodejs层控制，无需更改worker任何代码。
- 网友反馈：里面处理rtp抖动有问题，存下来的视频很容易花屏，缺帧等等等等。

### 流程走读
1. 客户端点击【开始录制】，对每个peer开始录制和结束录制

```
//case 'start-record':
//      return await handleStartRecordRequest(jsonMessage);

const handleStartRecordRequest = async (jsonMessage) => {
  console.log('handleStartRecordRequest() [data:%o]', jsonMessage);
  const peer = peers.get(jsonMessage.sessionId);

  if (!peer) {
    throw new Error(`Peer with id ${jsonMessage.sessionId} was not found`);
  }

  startRecord(peer);
};
```

2. 遍历每个producer录制

```
const startRecord = async (peer) => {
  let recordInfo = {};

  for (const producer of peer.producers) {
    recordInfo[producer.kind] = await publishProducerRtpStream(peer, producer);
  }

  recordInfo.fileName = Date.now().toString();

  peer.process = getProcess(recordInfo);

  setTimeout(async () => {
    for (const consumer of peer.consumers) {
      // Sometimes the consumer gets resumed before the GStreamer process has fully started
      // so wait a couple of seconds
      await consumer.resume();
      await consumer.requestKeyFrame();
    }
  }, 1000);
};
```

3. 支持两种工具录制ffmpeg和gstreamer
```
// Returns process command to use (GStreamer/FFmpeg) default is FFmpeg
const getProcess = (recordInfo) => {
  switch (PROCESS_NAME) {
    case 'GStreamer':
      return new GStreamer(recordInfo);
    case 'FFmpeg':
    default:
      return new FFmpeg(recordInfo);
  }
};
```

4. 录制方法是用 plainRtpTransport，把流发给一个127.0.0.1udp端口，然后ffmpeg命令行或者gstream就收处理

```
const publishProducerRtpStream = async (peer, producer, ffmpegRtpCapabilities) => {
  console.log('publishProducerRtpStream()');

  // Create the mediasoup RTP Transport used to send media to the GStreamer process
  const rtpTransportConfig = config.plainRtpTransport;

  // If the process is set to GStreamer set rtcpMux to false
  if (PROCESS_NAME === 'GStreamer') {
    rtpTransportConfig.rtcpMux = false;
  }

  const rtpTransport = await createTransport('plain', router, rtpTransportConfig);

  // Set the receiver RTP ports
  const remoteRtpPort = await getPort();
  peer.remotePorts.push(remoteRtpPort);

  let remoteRtcpPort;
  // If rtpTransport rtcpMux is false also set the receiver RTCP ports
  if (!rtpTransportConfig.rtcpMux) {
    remoteRtcpPort = await getPort();
    peer.remotePorts.push(remoteRtcpPort);
  }


  // Connect the mediasoup RTP transport to the ports used by GStreamer
  await rtpTransport.connect({
    ip: '127.0.0.1',
    port: remoteRtpPort,
    rtcpPort: remoteRtcpPort
  });

  peer.addTransport(rtpTransport);

  const codecs = [];
  // Codec passed to the RTP Consumer must match the codec in the Mediasoup router rtpCapabilities
  const routerCodec = router.rtpCapabilities.codecs.find(
    codec => codec.kind === producer.kind
  );
  codecs.push(routerCodec);

  const rtpCapabilities = {
    codecs,
    rtcpFeedback: []
  };

  // Start the consumer paused
  // Once the gstreamer process is ready to consume resume and send a keyframe
  const rtpConsumer = await rtpTransport.consume({
    producerId: producer.id,
    rtpCapabilities,
    paused: true
  });

  peer.consumers.push(rtpConsumer);

  return {
    remoteRtpPort,
    remoteRtcpPort,
    localRtcpPort: rtpTransport.rtcpTuple ? rtpTransport.rtcpTuple.localPort : undefined,
    rtpCapabilities,
    rtpParameters: rtpConsumer.rtpParameters
  };
};
```