# mediasoup新版本的管道协议变化
> 2022-08-30版本3.10.6：与worker管道通讯协议变化
1. 以前是json协议，现在是逗号分隔的字符串协议
![](.mediasoup新版本的管道协议变化_images/983c9c51.png)
2. 以前有个internal字段，现在变成handlerid字段
![](.mediasoup新版本的管道协议变化_images/2d97a98b.png)
3. 官方的日志：https://github.com/versatica/mediasoup/blob/v3/CHANGELOG.md