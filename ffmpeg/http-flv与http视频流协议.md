# http-flv与http视频流协议

## 一. 首先读一读这些文档
1. http-flv协议：https://blog.csdn.net/King_weng/article/details/106080645
1. http-flv-chunk：https://www.cnblogs.com/vczf/p/14813438.html
1. http-flv-srs：http://www.ossrs.net/lts/zh-cn/docs/v5/doc/delivery-http-flv

## 二. http视频流协议-核心原理
1. HTTP协议中有个约定：
   - content-length字段，http的body部分的长度服务器回复http请求的时候如果有这个字段，客户端就接收这个长度的数据然后就认为数据传输完成了。
   - 如果服务器回复http请求中没有这个字段，客户端就一直接收数据，直到服务器跟客户端的socket连接断开。
   - 原文链接：https://blog.csdn.net/King_weng/article/details/106080645
2. 总结：任何视频流都能用http传输：如http-flv，http-ts，http-mp4，http-mp3等等

## 三. http-mp4流比较特殊
1. 我遇到http-mp4流，ffmpeg对mp4需要seek，所以需要ffmpeg开启http range请求,http server也需要支持http range请求。
   - ffmpeg开启http range请求： ./ffmpeg -re -seekable 1 -i http://xxxxx/xxx.mp4 -c copy -f null null
2. 关于mp4的特点不适合做直播：
   - 经常要根据moov（帧索引或者目录）seek到指定位置，http直播流seek只能在请求头添加range参数，并不是所有服务器和客户端都值得很好。
   - mp4格式比较特殊：每一帧的在文件的位置和时间戳等信息都有登记在moov里面，所以写mp4要更新moov，和moov的存储位置这些都很麻烦。
   - 解析mp4也要先找到moov，然后seek很多次
   - 优点是有moov的索引， 容易做精准的定位，比如seek。 flv等seek估计要从头到尾遍历，mp4直接fseek搞定。
   - ts flv rtmp rtp h264附录b 这些格式一路向前就可以，而mp4可能需要回头看，可能还需要回头看的很远
3. http-mp4
   - 有些厂商的http-mp4，利用了moov，每次读到moov就seek到开头，让客户端循环拉流一个mp4文件，实现一个循环流




