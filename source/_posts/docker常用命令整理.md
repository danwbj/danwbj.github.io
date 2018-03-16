---
title: docker常用命令整理
date: 2018-03-16 14:15:35
tags:
---
#### docker信息
```bash
# 查看docker版本
$ docker version

# 查看docker系统信息
$ docker info
```
#### 镜像
```bash
# 查看镜像列表
$ docker images

# 删除镜像
$ docker rmi name/id

# 基于Dockerfile文件build镜像
$ docker  build [options] 镜像名 .  
注意： .代表Dockerfile文件在当前路径
    -t 为容器重新分配一个伪终端

# 创建一个新的容器并跑一个镜像
docker run [options]  image 
    -i 以交互模式运行
    -t 为容器重新分配一个伪终端
    -d 运行到后台
    -p 80:80	端口映射
    --name xxx 为容器指定一个名称

# 保存images为一个tar文件
$ docker save image_name -o name.tar
或者
$ docker save image_name > name.tar

#从一个tar文件加载images
$ docker load < name.tar

```
 
#### 容器
```bash
# 显示正在运行的容器
$ docker ps
    -a 显示所有容器，包含不是运行中的容器

# 停止运行中的容器
$ docker stop 容器name/id

#删除容器
$ docker rm 容器name/id

#进入到某个容器中
$ docker exec -it 容器name/id /bin/bash	

#通过差异性创建一个新的image
$ docker commit 容器id 新的镜像名
eg: docker commit bf2eff778794 dandanwu/testimage:v2	

```

#### Dockerfile编写
DockerFile分为四部分组成：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令
```bash
#第一行必须指令基于的基础镜像
FROM ubutu

#维护者信息
MAINTAINER docker_user  docker_user@mail.com

#使用哪个用户跑container 
USER

#将文件<src>拷贝到container的文件系统对应的路径<dest>
COPY file.js /home/file.js

#将文件<src>拷贝到container的文件系统对应的路径<dest>
ADD file.js /home/file.js

#在终端中执行命令
RUN apt-get update && apt-get install -y ngnix 
RUN echo "\ndaemon off;">>/etc/ngnix/nignix.conf

#container内部服务开启的端口，主机上要用还得在启动container时，做host-container的端口映射
EXPOSE 8017

#设置环境变量
ENV

#切换目录用，可以多次切换(相当于cd命令)，对RUN,CMD,ENTRYPOINT生效
WORKDIR ../

#可以将本地文件夹或者其他container的文件夹挂载到container中。
VOLUME

#容器启动时执行指令
#一个Dockerfile中只能有一条CMD命令，多条则只执行最后一条CMD
#可替换性：当docker run command的命令匹配到CMD command时，会替换CMD执行的命令。
CMD /usr/sbin/ngnix

#需要执行多条命令的时候可以像下边这样写
CMD [ "cmd1", "cmd2", "cmd3" ]

#container启动时执行的命令，但是一个Dockerfile中只能有一条ENTRYPOINT命令，如果多条，则只执行最后一条
#ENTRYPOINT没有CMD的可替换特性
ENTRYPOINT /usr/sbin/ngnix

```

