# ffmpeg的编译
>所有编译问题都可以分析configure源码找到答案

#说明
#1. 第三方库全部安装到默认的系统目录/usr/local
#2. 没有packet-config的可能需要指定库目录和头文件目录

#x264
#git clone https://code.videolan.org/videolan/x264.git
#configure --enable-static
#sudo make install

#fdk-aac
#git clone https://github.com/mstorsjo/fdk-aac.git
#./autogen.sh &&  configure --enable-static=yes --enable-shared=no
#sudo make install

#编译mp3
#wget https://zenlayer.dl.sourceforge.net/project/lame/lame/3.100/lame-3.100.tar.gz
#tar -xvf lame-3.100.tar.gz
#./configure --enable-static=yes --enable-shared=no
#make -j36 install

#编译 openssl
#wget https://www.openssl.org/source/openssl-1.1.1u.tar.gz
#./config no-shared
#make -j36 install
#pkg-config --libs openssl

#png
#sudo apt install libpng-tools

#nvenc的中间库
#git clone https://github.com/FFmpeg/nv-codec-headers.git
#git reset 450f8a6
#sudo make install

#yuv
#git clone https://chromium.googlesource.com/libyuv/libyuv
#mkdir build && cd build && cmake .. && sudo make install
#rm /usr/local/lib/libyuv.so

#wget https://sdk.lunarg.com/sdk/download/1.2.189.0/linux/vulkansdk-linux-x86_64-1.2.189.0.tar.gz?Human=true -O vulkansdk-linux-x86_64-1.2.189.0.tar.gz
#tar -xf vulkansdk-linux-x86_64-1.2.189.0.tar.gz
#export VULKAN_SDK=$(pwd)/1.2.189.0/x86_64

#ncnn
#git clone https://github.com/Tencent/ncnn.git
#git submodule update --init
#cmake .. -DNCNN_BUILD_TOOLS=OFF -DNCNN_VULKAN=OFF -DCMAKE_INSTALL_PREFIX=/usr/local -DNCNN_SHARED_LIB=OFF  && sudo make install
#rm /usr/local/lib64/libncnn.so*
#gpu静态库: -L../1.2.189.0/x86_64/lib -I../1.2.189.0/x86_64/include 
#-lvulkan -lglslang -lMachineIndependent -lOGLCompiler -lOSDependent -lSPIRV -lvku -lshaderc -lshaderc_combined -lshaderc_util

cd ffmpeg

#--enable-cuda-nvcc --enable-libwebp

./configure \
    --disable-shared \
    --enable-nvenc \
    --enable-cuvid \
    --enable-nonfree\
    --enable-libx264 \
    --enable-libfdk-aac \
    --enable-openssl \
    --enable-gpl \
    --enable-libmp3lame \
    --enable-protocol=https \
    --enable-encoder=png \
    --enable-decoder=png \
    --extra-ldflags=""  \
    --extra-cxxflags="" \
    --extra-libs="-lyuv -lstdc++ -lncnn -fopenmp -lm -ldl" \
    --disable-avdevice

cd ..
