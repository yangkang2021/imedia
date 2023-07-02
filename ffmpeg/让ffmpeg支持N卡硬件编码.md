# 让ffmpeg支持N卡硬件编解码

## 零. 关于nv-codec-headers库
>https://github.com/FFmpeg/nv-codec-headers.git
1. 这是一个非常巧妙的库
2. 它用【动态库的动态加载技术】实现了：解除了ffmpeg对n卡硬件加速动态库的强依赖。
3. 达到效果：
   - ffmpeg程序单文件部署
   - 部署不需要和libcuda.so,libnvcuvid.so,libnvidia-encode.so等so一起打包。
   - 运行不需要配置LD_LIBRARY_PATH
   - 如果用户指定要使用硬件编码，就会去系统找cuda和N卡驱动，找到就用，否则就报错。
   - 驱动和sdk让用户自己去独立安装
   - cpu和gpu版本的ffmpeg一个单文件ffmpeg搞定
   - 在没有N卡环境的电脑上也能编译gpu版本的ffmpeg
4. 实现原理：
   - 这个库只有头文件，自身不需要编译。
   - 头文件里面实现了cuda_load_functions函数
   - 底层用动态加载的N卡的动态库
   - windows用LoadLibrary/GetProcAddress/FreeLibrary
   - linux用dlopen/dlsym/dlclose
   - ffmpeg include这些头文件的时候cuda_load_functions被编译到ffmpeg
5. 扩展：
   - c++插件开发模型：通常就是基于【动态库的动态加载技术】
   - 比如obs，osg，libuv等。
6. 看看代码：dynlink_loader.h
   ```
   #if defined(_WIN32) || defined(__CYGWIN__)
   # define CUDA_LIBNAME "nvcuda.dll"
   # define NVCUVID_LIBNAME "nvcuvid.dll"
   # if defined(_WIN64) || defined(__CYGWIN64__)
   #  define NVENC_LIBNAME "nvEncodeAPI64.dll"
   # else
   #  define NVENC_LIBNAME "nvEncodeAPI.dll"
   # endif
   #else
   # define CUDA_LIBNAME "libcuda.so.1"
   # define NVCUVID_LIBNAME "libnvcuvid.so.1"
   # define NVENC_LIBNAME "libnvidia-encode.so.1"
   #endif
   
   #if !defined(FFNV_LOAD_FUNC) || !defined(FFNV_SYM_FUNC)
   # ifdef _WIN32
   #  define FFNV_LOAD_FUNC(path) LoadLibrary(TEXT(path))
   #  define FFNV_SYM_FUNC(lib, sym) GetProcAddress((lib), (sym))
   #  define FFNV_FREE_FUNC(lib) FreeLibrary(lib)
   # else
   #  include <dlfcn.h>
   #  define FFNV_LOAD_FUNC(path) dlopen((path), RTLD_LAZY)
   #  define FFNV_SYM_FUNC(lib, sym) dlsym((lib), (sym))
   #  define FFNV_FREE_FUNC(lib) dlclose(lib)
   # endif
   #endif
   
   static inline int cuda_load_functions(CudaFunctions **functions, void *logctx)
   {
       GENERIC_LOAD_FUNC_PREAMBLE(CudaFunctions, cuda, CUDA_LIBNAME);
   
       LOAD_SYMBOL(cuInit, tcuInit, "cuInit");
       ...
   ```

## 一.环境安装
1. 驱动：直接去官网下载最新
2. cuda：发现linux驱动自带，不用安装了
3. VideoSdk：发现linux驱动自带，不用安装了
4. nv-codec-headers
   - 从动态库生成的头文件，支持Windows和linux
    ```
    https://github.com/FFmpeg/nv-codec-headers.git
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

