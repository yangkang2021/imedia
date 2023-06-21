# 从webrtc模块独立的项目列表

## TODO
1. 想做这事： 把webrtc的带宽估计，拥塞控制独立成一个开源项目。
   - 实现一个功能像 kcp，udt一样的库，在搭配一些demo。 
   - 让webrtc的gcc用到各行各业
   - 带上jitterbuffer+PacerSend
   - 以mediasoup的基础开始做

## 一. 带宽估计，拥塞控制
1. https://github.com/yuanrongxi/razor 做的不好
2. mediasoup：实现了一套google cc，代码简单

## 二. p2p库
1. libice
   - 它实现了RFC5245规范定义的交互式连接建立(ICE)协议
   - https://github.com/str2num/libice
1. cdnbye
1. libjuice
   - Lightweight UDP Interactive Connectivity Establishment (ICE) library
   - https://github.com/paullouisageneau/libjuice
1. libdatachannel
   - a standalone implementation of WebRTC Data Channels, WebRTC Media Transport, and WebSockets
   - https://github.com/paullouisageneau/libdatachannel
3. PJNATH
   - Open Source ICE, STUN, and TURN Library
   - https://www.pjsip.org/pjnath/docs/html/index.htm
1. Libjingle：webrtc的前身，不在维护

## 三. 音频
1. https://chromium.googlesource.com/chromiumos/third_party/webrtc-apm
1. https://gitlab.freedesktop.org/pulseaudio/webrtc-audio-processing