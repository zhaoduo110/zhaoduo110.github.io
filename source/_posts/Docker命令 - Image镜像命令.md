---
title: Docker命令 - Image镜像命令
abbrlink: 485f33c3
date: 2021-11-24 15:51:24
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-x8k5wz.png
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-x8k5wz.png
---



> docker images

查看本机所有的镜像，等于`docker image ls` 

参数

-a, --all  列出所有镜像

-q, --quiet   只显示镜像id

![image-20211124160018698](https://could-res-1252778021.file.myqcloud.com/img/20211124160019.png)



> docker search 镜像名

搜索镜像



> docker pull 镜像名[:tag]

下载/拉取镜像到本地，等于 `docker image pull`。只有镜像名默认下载的tag是lastest

![image-20211124160550555](https://could-res-1252778021.file.myqcloud.com/img/20211124160551.png)



> docker rmi imageId

删除本机的镜像，等于 `docker image rm `

```shell
docker rmi -f 镜像id #删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id 镜像id#删除指定的镜像
docker rmi -f $(docker images -aq) #删除全部的镜像
```









