# mediasoup-workder的cpp文件列表

```
src/
├── Channel
│   ├── ChannelNotifier.cpp
│   ├── ChannelRequest.cpp
│   └── ChannelSocket.cpp
├── DepLibSRTP.cpp
├── DepLibUV.cpp
├── DepLibWebRTC.cpp
├── DepOpenSSL.cpp
├── DepUsrSCTP.cpp
├── handles
│   ├── SignalsHandler.cpp
│   ├── TcpConnectionHandler.cpp
│   ├── TcpServerHandler.cpp
│   ├── Timer.cpp
│   ├── UdpSocketHandler.cpp
│   └── UnixStreamSocket.cpp
├── lib.cpp
├── lib.rs
├── Logger.cpp
├── main.cpp
├── MediaSoupErrors.cpp
├── PayloadChannel
│   ├── Notification.cpp
│   ├── PayloadChannelNotifier.cpp
│   ├── PayloadChannelRequest.cpp
│   └── PayloadChannelSocket.cpp
├── RTC
│   ├── ActiveSpeakerObserver.cpp
│   ├── AudioLevelObserver.cpp
│   ├── Codecs
│   │   ├── H264.cpp
│   │   ├── H264_SVC.cpp
│   │   ├── Opus.cpp
│   │   ├── VP8.cpp
│   │   └── VP9.cpp
│   ├── Consumer.cpp
│   ├── DataConsumer.cpp
│   ├── DataProducer.cpp
│   ├── DirectTransport.cpp
│   ├── DtlsTransport.cpp
│   ├── IceCandidate.cpp
│   ├── IceServer.cpp
│   ├── KeyFrameRequestManager.cpp
│   ├── NackGenerator.cpp
│   ├── PipeConsumer.cpp
│   ├── PipeTransport.cpp
│   ├── PlainTransport.cpp
│   ├── PortManager.cpp
│   ├── Producer.cpp
│   ├── RateCalculator.cpp
│   ├── Router.cpp
│   ├── RTCP
│   │   ├── Bye.cpp
│   │   ├── CompoundPacket.cpp
│   │   ├── Feedback.cpp
│   │   ├── FeedbackPs.cpp
│   │   ├── FeedbackPsAfb.cpp
│   │   ├── FeedbackPsFir.cpp
│   │   ├── FeedbackPsLei.cpp
│   │   ├── FeedbackPsPli.cpp
│   │   ├── FeedbackPsRemb.cpp
│   │   ├── FeedbackPsRpsi.cpp
│   │   ├── FeedbackPsSli.cpp
│   │   ├── FeedbackPsTst.cpp
│   │   ├── FeedbackPsVbcm.cpp
│   │   ├── FeedbackRtp.cpp
│   │   ├── FeedbackRtpEcn.cpp
│   │   ├── FeedbackRtpNack.cpp
│   │   ├── FeedbackRtpSrReq.cpp
│   │   ├── FeedbackRtpTllei.cpp
│   │   ├── FeedbackRtpTmmb.cpp
│   │   ├── FeedbackRtpTransport.cpp
│   │   ├── Packet.cpp
│   │   ├── ReceiverReport.cpp
│   │   ├── Sdes.cpp
│   │   ├── SenderReport.cpp
│   │   ├── XR.cpp
│   │   ├── XrDelaySinceLastRr.cpp
│   │   └── XrReceiverReferenceTime.cpp
│   ├── RtpDictionaries
│   │   ├── Media.cpp
│   │   ├── Parameters.cpp
│   │   ├── RtcpFeedback.cpp
│   │   ├── RtcpParameters.cpp
│   │   ├── RtpCodecMimeType.cpp
│   │   ├── RtpCodecParameters.cpp
│   │   ├── RtpEncodingParameters.cpp
│   │   ├── RtpHeaderExtensionParameters.cpp
│   │   ├── RtpHeaderExtensionUri.cpp
│   │   ├── RtpParameters.cpp
│   │   └── RtpRtxParameters.cpp
│   ├── RtpListener.cpp
│   ├── RtpObserver.cpp
│   ├── RtpPacket.cpp
│   ├── RtpProbationGenerator.cpp
│   ├── RtpStream.cpp
│   ├── RtpStreamRecv.cpp
│   ├── RtpStreamSend.cpp
│   ├── RtxStream.cpp
│   ├── SctpAssociation.cpp
│   ├── SctpDictionaries
│   │   └── SctpStreamParameters.cpp
│   ├── SctpListener.cpp
│   ├── SenderBandwidthEstimator.cpp
│   ├── SeqManager.cpp
│   ├── SimpleConsumer.cpp
│   ├── SimulcastConsumer.cpp
│   ├── SrtpSession.cpp
│   ├── StunPacket.cpp
│   ├── SvcConsumer.cpp
│   ├── TcpConnection.cpp
│   ├── TcpServer.cpp
│   ├── Transport.cpp
│   ├── TransportCongestionControlClient.cpp
│   ├── TransportCongestionControlServer.cpp
│   ├── TransportTuple.cpp
│   ├── TrendCalculator.cpp
│   ├── UdpSocket.cpp
│   ├── WebRtcServer.cpp
│   └── WebRtcTransport.cpp
├── Settings.cpp
├── Utils
│   ├── Crypto.cpp
│   ├── File.cpp
│   ├── IP.cpp
│   ├── README_BASE64_UTILS
│   └── String.cpp
└── Worker.cpp

9 directories, 119 files

```