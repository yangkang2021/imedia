# Webassembly--一项令人兴奋的技术

### （一）为什么要学习Webassembly技术
1. 把c++编译成js提升性能
2. 复用c++几十年积累的技术、项目、软件，开发语言等。直接移植到js。
3. opencv.js，tf.js，ffmpeg.js，webcodec等技术已经用到了这些技术。
4. 基于c++开发的语言lua，python，java等可以编译成js。
5. 最重要的：自研算法、AI推理、shader渲染能直接js。

### （二）Webassembly技术方案
> emscripten(emsdk)，rust，golang，微软那个c#的Blazor都不错
- 既然主要目的是c++算法移植，首选emsdk。

### （三）Webassembly疑问？
>emsdk自成体系，所以没有源码的库无法使用
1. 怎么与js双向交互？这个是基础应该很容易
    - 调用js：EM_ASM，EM_JS，emscripten_run_script，emscripten::val::global("alert")("hi")
    - 导出js函数:bind和-s EXPORTED_FUNCTIONS="['_main','lerp']"
    ```
         // quick_example.cpp
        #include <emscripten/bind.h>
        
        using namespace emscripten;
        
        float lerp(float a, float b, float t) {
           return (1 - t) * a + t * b;
        }
        
        EMSCRIPTEN_BINDINGS(my_module) {
           function("lerp", &lerp);
        }
   ```
2. 是否支持任意平台编译和调用系统库，第三方库？ 不支持
3. 能否调用并行指令neon，sse？ 支持部分
4. 能否支持显卡编程cuda，opengl，d3d，内联汇编？ 不支持
5. opencv.js 和 tf.js 是怎么使用webassembly技术的？emsdk直接编译
6. WASM是怎样的二进制文件技术？
7. 怎么传递二进制byte数据？直接使用指针与js的array对应。

### (四) 这是一种c++扩展能力，还有很多：
1. jni
2. cgo
3. v8-addon
4. swing
5. webassembly

### （五） 学习笔记
1. https://webassembly.org/
2. https://emscripten.org/
3. https://emscripten.org/docs/getting_started/downloads.html

### (六) emsdk安装和使用
```
git clone https://github.com/emscripten-core/emsdk.git

cd emsdk

git pull

./emsdk install latest

./emsdk activate latest

source ./emsdk_env.sh

#编译，然后新建html加载main.js
emcc -o main.js -std=c++11 --bind main.cpp lib.cpp
```




