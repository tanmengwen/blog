---
layout: post
title:  "How to use docker"
date:   2018-7-7 10:45:13 -0400
tag: jekyll
---

### Ubuntu 14.04 安装 Docker
- https://github.com/gatieme/AderXCoding/tree/master/system/tools/docker

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
apt-cache madison docker-ce
sudo apt-get install docker-ce
docker -v
sudo service docker start

# 取消sudo权限
sudo usermod -a -G docker $USER
```

### Containers

- https://docs.docker.com/get-started/part2/

### Docker 命令大全
- http://www.runoob.com/docker/docker-command-manual.html

### 常用命令

```
docker images   #查看所有的镜像
docker ps       #查看所有正在运行的容器
docker ps -a    #查看所有镜像以及对应的容器名

docker pull davvdg/ros-kalibr # 从Docker Hub下载ros kalibr镜像
docker build -t ubuntu14.04 . # 使用当前目录的Dockerfile创建ubuntu的镜像
docker build github.com/creack/docker-firefox # 使用URL github.com/creack/docker-firefox 的 Dockerfile 创建镜像

docker start container-name
docker stop container-name
docker stop container-id
docker rm -v container-name

docker exec -i -t  mynginx /bin/bash  # 在容器mynginx中开启一个交互模式的终端
docker run -it nginx:latest /bin/bash  # 使用镜像nginx:latest以交互模式启动一个容器,在容器内执行/bin/bash命令
docker run --name mynginx -d nginx:latest # 使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx
docker run --name mynginx -it nginx:latest # 使用docker镜像nginx:latest交互式命令启动一个容器,并将容器命名为mynginx

docker run -v /home/tanmengwen/disk1/docker-data:/data --name="kalibr" -it davvdg/ros-kalibr # 挂载本地目录到docker容器里面

docker tag ubuntu:14.04 tmw/ubuntu:v1 # 将镜像ubuntu:14.04标记为 tmw/ubuntu:v1 镜像
docker save -o my_ubuntu_v1.tar tmw/ubuntu:v1 # 将镜像tmw/ubuntu:v1 生成my_ubuntu_v3.tar文档

# Commit an existing container 提交一个存在的容器
$ sudo docker ps
ID       IMAGE         COMMAND          CREATED       STATUS           PORTS
c3f279d17e0a        ubuntu:12.04/bin/bash           7 days ago          Up25 hours
197387f1b436        ubuntu:12.04/bin/bash           7 days ago          Up25 hours
$ docker commit c3f279d17e0a  SvenDowideit/testimage:version3
f5283438590d
$ docker images | head
REPOSITORY                        TAG                 ID                  CREATED             VIRTUAL SIZE
SvenDowideit/testimage            version3            f5283438590d        16 seconds 
```

### My Images

```
docker pull tanmengwen/ros-kalibr

mkdir -p ~/docker-data/data
cp chessboard.yaml ~/docker-data
cp -r calib-data-images ~/docker-data/data

docker run -v ~/docker-data:/data --name="kalibr" -d tanmengwen/ros-kalibr
./calib_cam.sh

# then you will find calibation result in ~/docker-data
```
