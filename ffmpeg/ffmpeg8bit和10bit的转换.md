# ffmpeg8bit和10bit的转换


8bit还是10bit，只是一种像素格式， 指定格式就能转: 如-pix_fmt yuv420p 或者 -pix_fmt yuv444p10be或者 -pix_fmt yuv444p10le
```
    PIX_FMT_YUV420P10BE,///< planar YUV 4:2:0, 15bpp, (1 Cr & Cb sample per 2x2 Y samples), big-endian 
    PIX_FMT_YUV420P10LE,///< planar YUV 4:2:0, 15bpp, (1 Cr & Cb sample per 2x2 Y samples), little-endian 
```