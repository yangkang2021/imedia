# 字幕
>从传输协议、封装格式、主流软件等等方面分享字幕流

### 一. 三种字幕存储方式
1. 外挂字幕--独立于视频文件的字幕文件，如s.srt
2. 软字幕--以流的形式存在视频文件里面。
3. 硬字幕--draw到每帧里面。

## 二. 两种字幕类型
1. 文本字幕： ass，srt
2. 图像字幕：dvbsub， dvdsub

## 三. 支持字幕的封装格式
1. mp4
2. mkv
3. ts
4. 其他

## 四. 支持字幕的协议
1. ts
2. 其他

## 五. 支持字幕的播放器
1. ffplay只支持图像字幕
2. pc的播放器一把都支持各种字幕：vlc，pot，迅雷
3. 移动端一般都不支持软字幕和外挂字幕

## 七. 最常见需求：外挂字幕/软字幕 转 硬字幕
> https://trac.ffmpeg.org/wiki/HowToBurnSubtitlesIntoVideo

1. 外挂字幕转 硬字幕的方案
    ```
    ffplay -vf "subtitles=a.srt" a.mp4
   ```
1. 软字幕 转 硬字幕的方案，只能处理图片类型的字幕,图片格式的字幕 用 overlay就搞定
    ```
    ffmpeg -i "xxx.ts" -filter_complex "[0:v][0:s]overlay[v]" -map "[v]" -map 0:a:0 -vcodec libx264 x.ts
    ```
1. ffmepg字幕类型转换
    ```
   Subtitle encoding currently only possible from text to text or bitmap to bitmap
   ```
1. 看看ffmpeg支持的字幕编解码器
    ```
    ffmpeg -encoders 
    ffmpeg -decoders
    S..... ssa                  ASS (Advanced SubStation Alpha) subtitle (codec ass)
    S..... ass                  ASS (Advanced SubStation Alpha) subtitle
    S..... dvbsub               DVB subtitles (codec dvb_subtitle)
    S..... dvdsub               DVD subtitles (codec dvd_subtitle)
    S..... cc_dec               Closed Caption (EIA-608 / CEA-708) (codec eia_608)
    S..... pgssub               HDMV Presentation Graphic Stream subtitles (codec hdmv_pgs_subtitle)
    S..... jacosub              JACOsub subtitle
    S..... microdvd             MicroDVD subtitle
    S..... mov_text             3GPP Timed Text subtitle
    S..... mpl2                 MPL2 subtitle
    S..... pjs                  PJS subtitle
    S..... realtext             RealText subtitle
    S..... sami                 SAMI subtitle
    S..... stl                  Spruce subtitle format
    S..... srt                  SubRip subtitle (codec subrip)
    S..... subrip               SubRip subtitle
    S..... subviewer            SubViewer subtitle
    S..... subviewer1           SubViewer1 subtitle
    S..... text                 Raw text subtitle
    S..... vplayer              VPlayer subtitle
    S..... webvtt               WebVTT subtitle
    S..... xsub                 XSUB
   ```
## 八. 参考
- https://www.freesion.com/article/52981414648
- https://blog.csdn.net/qq_39969226/article/details/106644753
