# webrtc mediasoup集成人工智能与Pipeline
>采集分析渲染编码 + 解码分析渲染 都是全程GPU是最完美的pipeline

### 在什么地方做视频分析：

在视频会议里面加入 美颜，人像分割，背景替换，人脸检测，识别... 对性能要求非常高：资源使用率本来就高，而且最多给5ms的时间。
- 要把视频分析和 gpu硬件编码和gpu渲染结合，在opengl/metal/d3d渲染的同时分析
- 编解码和渲染，yuv数据都可能经过显卡gpu，在这个时候处理 
- obs的采集视频帧，是都要用首先上传到显卡里面去预览和合并。得到的启发就是：如果做实时的 视频编辑和处理，最好都用gpu完成。

这需要给 windows linux mac Android ios的采集模块加一个中间层：
1. 采集一般是cpu。渲染和编码一般都是gpu。
2. 如果有视频编辑、滤镜、分析等，应该从渲染gpu下载yuv。
3. gpu渲染的yuv最好直接给到GPU。
总之要重写webrtc自带的渲染和编码工厂逻辑。采集渲染处理编码在gpu一气呵成最完美。
类似GPUImage，类似obs

#### pc的的采集，渲染，编解码与GPU的交互
> 采集后在cpu得到yuv，考虑用dx的全流程gpu？
> 解码后得到的yuv在cpu，给opengl上传显存渲染


#### android的采集，渲染，编解码与GPU的交互
> 默认camera2采集后的数据就在显存，然后显存直接给mediacodec硬件编码，和给opengl渲染。
> 解码后得到的yuv在显存，直接渲染


#### ios的的采集，渲染，编解码与GPU的交互
> 采集后的数据在cpu，然后分别给编码器和opengl分别上传到显存处理。
> 解码后得到的yuv在cpu，给opengl上传显存渲染

所以，android用shader实现人工智能最好，其他端随意。

### 关于obs和GPUImage等pipeline
- obs是一个 
- 支持各种数据源(摄像头 文件 游戏 采集 文字，录屏，录窗口...)采集的 
- 支持简单缩放/位移/文字叠加/滤镜等处理的 
- 录制和直播推流软件。
- 可以输出yuv，h264，rtmp，mp4

obs和webrtc怎么结合的：
- 两种方式：在webrtc里面用libobs采集，在obs里面用webrtc推流
    1. 把yuv放到webrtc的track就能支持基本所有数据的输入
    2. 很多人反过来，在obs内部实现一个webrtc output serveice。 就拥有了webrtc的直播软件
        - 你需要obs的界面就这样用，但是大多数webrtc都是视频通话，不是简单的直播推流，那就不能

