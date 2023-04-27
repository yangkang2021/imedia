# mediasoup弱网优化

[这篇文章](https://blog.csdn.net/qw225967/article/details/123897529) 对mediasoup sfu做了两个优化，整体的卡顿率都有大幅的下降：
1. 调整nack参数：时间间隔、最大重传次数，重传频繁度。
    - 将定时器间隔从 40ms/次 下调到 20ms/次；
    - 将最大重传次数从 10次 上调至 20次（还可以增大到40次）；
    - 最后是根据 rtt 我们重传次数越多的包要的越频繁。
2. 推流侧支持flexfec：往mediasoup加入了webrtc的flexfec的解码代码。

最重要的是，在10%丢包的场景下，已经基本实现了无感知的优化（接收端不做快慢放，不增加jitter）。