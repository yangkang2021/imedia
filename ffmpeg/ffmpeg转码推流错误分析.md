# ffmpeg转码推流错误分析


## 一. 推流出错的原因简单汇总：
1. 推流参数配置错误：copy和 profile冲突，需要手动去改推流命令
    - Undefined constant or missing '(' in 'high'
    - Unable to parse option value "high"
    - Error setting option profile to value high.
2. h265忘了转码：需要手动去改推流命令
    - Video codec hevc not compatible with flv
    - Could not write header for output file #0 (incorrect codec parameters ?)
3. 源商服务404： 需要联系源商处理
    -  HTTP error 404
4. 推流偶尔会断：正常的，会自动重连解决。
    - End of file
    - Connection reset by peer
    - Conversion failed!
    - av_interleaved_write_frame(): Broken pipe
5. 转码偶尔出错：ffmpeg会丢弃帧，没问题。
   - Error parsing ADTS frame header!
   - aac bitstream error
   - non-existing PPS 0 referenced
   - decode_slice_header error
   - concealing 1421 DC, 1421 AC, 1421 MV errors in P frame
   - error while decoding MB 68 27, bytestream -7

## 二. 处理办法：
1. 上面：1，2需要去改推流命令，3要反馈给源商，4，5会自动恢复
2. udp，rtp，rtsp还有不确定编码格式的源都必须转码，不能copy，因为ffmpeg转码有码流修复功能，不然花屏灰屏灰会到用户那里。
3. 每一路转码，大概需要1个cpu核心。 缩放/裁剪/水印可能还需要一个cpu核。