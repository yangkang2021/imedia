# 让ffmpeg支持N卡硬件编解码

## 一.环境安装
1. 驱动：直接去官网下载最新
2. cuda：发现linux驱动自带，不用安装了
3. VideoSdk：发现linux驱动自带，不用安装了
4. nv-codec-headers
   - 从动态库生成的头文件，支持Windows和linux
    ```
    https://git.videolan.org/git/ffmpeg/nv-codec-headers.git
    ```
5. nvidia-patch：该补丁消除了Nvidia对消费级GPU施加的同时进行NVENC视频编码会话的最大数量的限制
   ```
   OpenEncodeSessionEx failed: out of memory (10): (no details)
   ```
   ```
   https://github.com/keylase/nvidia-patch
   ```
## 二. 配置和编译ffmpeg
   ```
   ./configure --enable-nvenc --enable-cuvid
   ```
## 三. 测试性能
测了一下E7 40核cpu + 4090显卡的性能：
   - 4090: 可以同时解码和编码60路1080p，达到2000fps
   - E7 40核心: 可以同时解码和编码30路1080p, 到达800fps
   - 如果是720P, 参数翻倍。
## 四. 参考资料
- [FFmpeg引入NVIDIA硬件编解码扩展](https://ffmpeg.xianwaizhiyin.net/compile-ffmpeg/nvidia.html)
- [FFmpeg硬件加速](https://www.xianwaizhiyin.net/?p=642)
