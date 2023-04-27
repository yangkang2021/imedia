使用docker的意义：
隔离环境：解决编译环境与运行环境的系统和版本的不同。 
全部配置：可以把一切都项目配置好了在发布。 
集体打包：多应用集合打包。如：redis，tomcat，nginx合并在一起。
高效仓库：便于高速的下载和部署。
便于管理：配合k8s。

(一)参考网址
视频课程地址：https://www.bilibili.com/video/av27122140
官网：https://www.docker.com/
官方镜像仓库服务，可以搜镜像：https://hub.docker.com/。太慢请用阿里云。
阿里云提供：个人或者企业私有镜像服务 + 方法镜像服务hub的加速。https://cr.console.aliyun.com
--------------------------------------------------------------------------------
(二)docker架构

基本概念：镜像/容器/docker客户端/docker主机/docker守护进程daemon/仓库服务registry/仓库repository。
容器是用镜像创建的运行实例，把容器看作简易的linux。如运行10个tomcat镜像的实例。
registry与repository的区别：registry是管理各种镜像的服务，repository是管理一种镜像的各个版本。
镜像是容器模版。image文件，由一层一层的文件系统叠加组成，也就是联合文件系统。如tomcat镜像。

--------------------------------------------------------------------------------
(三)docker安装配置与Heloworld
安装docker：https://docs.docker.com/engine/install/centos/
sudo yum install -y yum-utils

sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io

sudo systemctl restart docker

配置阿里云镜像服务加速地址：阿里云官网有详细操作说明
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://uvajjyes.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

配置阿里云私有仓库地址：阿里云官网有详细操作说明
# 开发机打包镜像
sudo docker login --username=yangkang_taobao@163.com registry.cn-hangzhou.aliyuncs.com
sudo docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:[镜像版本号]
sudo docker push registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:0.2
# 线上部署
sudo docker pull registry.cn-hangzhou.aliyuncs.com/moming/srs-rtc:0.2

运行hello world：docker run hello-world:latest。
--------------------------------------------------------------------------------
(四)docker命令

docker的帮助命令三个：
docker version
docker info
docker --help


docker镜像命令-29个
docker images
docker search
docker pull 镜像名:TAG
docker rmi -f 镜像名:TAG
docker rmi -f $(docker images -qa) #删除全部镜像
docker commit -m="message" -a="yk" 容器id 镜像新名字:[TAG] #制作自己的镜像
docker push
docker history 镜像id


--------------------------------------------------------------------------------
docker容器命令-48个
#启动容器
docker run -it  镜像名字:[TAG]  #交互下运行docker，退出：ctrl+P+Q后台运行或者exit停止运行
docker run -d #后台运行
docker run -d centos /bin/sh -c "while true; do echo hello zzyy; sleep 2; done"
docker attach #进入交互
docker run -P #随机端口
docker run -p hostPort:containerPort #指定端口
docker run --name 指定别名
docker run -it -v 主机目录：容器目录:权限 镜像名。#数据持久化：两边自动创建目录

#容器运行管理
docker ps -l #列出运行的所有容器
docker start 容器id
docker restart 容器id
docker stop 容器id
docker kill 容器id
docker rm -f 容器id
docker rm -f $(docker ps -a -q)

#容器内部信息管理
docker logs -t -f --tail 3 容器id
docker top 容器id
docker inspect 容器id
docker exec -t 容器id ls -l /tmp
docker exec -it 容器id /bin/bash
docker cp 容器id:文件路径 主机目录


--------------------------------------------------------------------------------
容器数据卷命令：数据持久化，容器间的数据共享和重用
相当于给容器新插入1个移动硬盘
docker run -it -v 主机目录：容器目录:权限 镜像名。两边自动创建目录
用dockerfile 新建支持容器数据卷的镜像。docker build -f mydockerfile -t 镜像名 生成目录
多个容器共享数据卷，run 添加--volumes-from 容器名，新容器与老容器共享传递数据。
--------------------------------------------------------------------------------
(五)DockerFile
原则：
它是用来构建镜像的源文件，有一系列的命令和参数构成。
大写保留字指令 + 1-n个参数
一条指令形成一个新的镜像
保留字指令：
FROM ，scratch是最基础的镜像。
MAINTAINER：镜像维护者的姓名和邮箱
RUN：运行命令
EXPOSE：暴露端口
WORKDIR：工作目录
ENV：设置环境变量
ADD：拷贝+解压
COPY：拷贝
VOLUME：容器卷，数据拷贝
CMD：启动命令，只保留最后一个。
ENTRYPOINT：启动命令追加
ONBUILD：被子类继承的时候执行

DockerFile实战：
centos
修改默认路径
添加vim支持
添加ifconfig支持
centos
支持ip查询
ONBUILD
自己实现一个tomcat docker。
--------------------------------------------------------------------------------
(五)Docker运行容器
tomcat
mysql
redis
srs，tf，英伟达的docker
srs的docker部署
mediasoup的docker部署
英伟达的docker
先装驱动，然后装docker，无需安装cuda和cudnn。三种使用英伟达docker的方式：前两种已经废弃了
1. nvidia-docker：安装nvidia-docker，用命令nvidia-docker启动docker。
2. nvidia-docker v2：安装nvidia-docker2，用命令docker run --runtime=nvidia启动docker。
3. nvidia-container-toolkit：安装docker 19.03 及之后，nvidia-container-toolkit，用docker run --gpus all启动docker。
(六)Docker的性能
音视频与图像算法一直推崇c++原生开发以追求极致的性能；
在Docker时代，我们非常关心：Docker会带来性能损失吗？多核利用与多显卡计算有问题吗？
Docker有性能损失，但是很小，相对Docker的出色的功能，这点的性能损失是可以忽略不计的，Docker是一个开源的应用容器引擎，可以让开发者打包应用到一个容器中，然后发布到任何流行的Linux机器上运行。
https://www.cnblogs.com/idbeta/p/4982173.html