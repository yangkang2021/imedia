# mediasoup客户端创建producer流程


1. 创建Track：peerconn_factory->CreateVideoTrack(...)

2. 创建Produce：send_transport_->Produce(callback, track, &encodings, nullptr, codec_selected)
    - callback：创建过程中回调函数提供参数
    - encodings：码率，SVC参数，simulcast参数
    - codec_selected：指定编码器，不指定就是第一个，一般是vp8
   
3. 创建Produce回调: OnProduce(transportId，rtpParameters)，由send_transport_->Produce内部回调
   - 通知sfu创建producer：websocket_->Request("produce", data(transportId，rtpParameters));
   - 等待websocket返回producerId：在给回callback，要求返回std::future<std::string>