# 目录
> srs影响力确实牛逼，创造了无数生产力，就业岗位，项目和代码示范作用, 对其作者要充满敬意


# (零) 要点提炼
1. http服务：
1. http_server统计信息网站
1. http_api信令与统计信息接口等
   > https://ossrs.net/lts/zh-cn/docs/v4/doc/http-api
2. 集群

# (一) 下载编译运行srs
1. 下载编译运行
    ```
    git clone https://github.com/ossrs/srs.git
    cd srs/trunk
    make -j8
    ./objs/srs -c conf/srs.conf
    ps -ef | grep srs
    netstat -anp | grep srs
    lsof -i:1935
    tail -f ./objs/srs.log
    cat ./objs/srs.pid
    
    ps -ef | grep srs | grep -v grep | awk '{print $2}' | xargs kill -9
    
    https://192.168.0.200:8088/
    https://192.168.0.200:8088/console/
    https://192.168.0.200:8088/players/
    https://192.168.0.200:8088/demos/
    ```
2. 推拉流测试
    ```
    ffmpeg -re -i bipbop.mp4 -vcodec libx264 -acodec aac -f flv -y rtmp://192.168.0.200/live/livestream
    webrtc://192.168.0.200:1990/live/livestream?schema=https&eip=192.168.0.200
    
    http://123.57.87.177:8080/live/livestream.flv
    webrtc://123.57.87.177:1990/live/livestream?schema=https&eip=123.57.87.177
    
    ```
    ```
    rtmp://192.168.0.200/live/livestream
    http://192.168.0.200:8080/live/livestream.flv
    http://192.168.0.200:8080/live/livestream.m3u8
    webrtc://192.168.0.200:1990/live/livestream?schema=https&eip=192.168.0.200
    ```
3. 看看配置文件都有啥
    - rtmp监听1935，最多1000个连接
    - 在1985/1990端口开启了一个http api服务。
    - 在8080/8088端口开启了一个http server服务。
    - 其他略过。
4. 配置文件
    ```
    # main config for srs.
    # @see full.conf for detail config.
    
    listen              1935;
    max_connections     1000;
    srs_log_tank        file;
    srs_log_file        ./objs/srs.log;
    daemon              off;
    
    http_server {
        enabled         on;
        listen          8080;
        dir             ./objs/nginx/html;
        https {
            enabled on;
            listen 8088;
            key ./conf/server.key;
            cert ./conf/server.crt;
        }
    }
    
    http_api {
        enabled         on;
        listen          1985;
        https {
            enabled on;
            listen 1990;
            key ./conf/server.key;
            cert ./conf/server.crt;
        }
    }
    
    stats {
        network         0;
    }
    
    rtc_server {
        enabled on;
        listen 8000;
        candidate 192.168.0.200;
    }
    
    vhost __defaultVhost__ {
        hls {
            enabled     on;
        }
        http_remux {
            enabled     on;
            mount       [vhost]/[app]/[stream].flv;
        }
        dvr {
            enabled      off;
            dvr_path     ./objs/nginx/html/[app]/record/[stream]-[2006]-[01]-[02]-[15]-[04]-[05]-[999].flv;
            dvr_plan     segment;
            dvr_duration    30;
            dvr_wait_keyframe       on;
        }
        rtc {
            enabled on;
            nack on;
            twcc on;
    	rtc_to_rtmp on;
        }
    }
    ```
    
![xxx](C:/Users/MoMing/Desktop/learn_srs/images/configfile.png)
3. 用浏览器访问两个http服务试试：


--ignore-certificate-errors --allow-running-insecure-content --unsafely-treat-insecure-origin-as-secure="http://192.168.0.200:8080"

chrome://flags/#unsafely-treat-insecure-origin-as-secure

# (二) 代码预览

2. 看看目录结构
![xxx](C:/Users/MoMing/Desktop/learn_srs/images/file_list.png)

# (三) 编译运行


![xxx](C:/Users/MoMing/Desktop/learn_srs/images/build.png)




![xxx](C:/Users/MoMing/Desktop/learn_srs/images/rtc_sdk.png)

双向打通

# SRS资料汇总：
1. 代码
1. wiki：1.0，2.0，3.0，4.0
1. 视频：作者b站，零声两门，其他一些。
1. 公众号
1. 知识星球
2. 

协程机制：https://blog.csdn.net/charles1e/article/details/83625038


# Docker

```
编译
docker build -f Dockerfile -t moming/srs-rtc:0.2 .

运行：
docker run -d\
-p 1935:1935 \
-p 1990:1990 \
-p 8088:8088 \
-p 1985:1985 \
-p 8080:8080 \
-p 8000:8000/udp \
moming/srs-rtc:0.2

docker run -d \
-p 1935:1935 \
-p 1990:1990 \
-p 8088:8088 \
-p 1985:1985 \
-p 8080:8080 \
-p 8000:8000/udp \
registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:0.2
```


```
docker exec -it $(docker ps -q) /bin/bash

sudo docker login --username=yangkang_taobao@163.com registry.cn-hangzhou.aliyuncs.com

sudo docker tag  $(docker images -f "reference=moming/srs-rtc:0.2" -q) registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:0.2

sudo docker push registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:0.2

sudo docker pull registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:0.2

sudo systemctl restart docker

centos7无法联网
临时关闭
systemctl stop NetworkManager
永久关闭
systemctl disable NetworkManager
重启
systemctl restart network
```

