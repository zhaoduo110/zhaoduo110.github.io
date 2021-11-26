---
title: Docker命令 - Container run 标识
abbrlink: db6df65c
date: 2021-11-17 14:51:05
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20211117172402.png
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20211117172402.png
---







# 容器标识

>  --name='container_name'：参数设置容器的名称，如果不指定，docker在生成容器时会随机生成一个唯一的标识符做为容器的名称

> --cidfile='file_path'：将容器ID写入文件

容器本身的标识总共有三种：长的容器ID、短的容器ID、容器名称



# 镜像标识

>  Image[:tag]

比如 nginx:1.21

![image-20211104181619020](https://could-res-1252778021.file.myqcloud.com/img/20211104181620.png)

> Image Id

![image-20211104181725442](https://could-res-1252778021.file.myqcloud.com/img/20211104181725.png)



> Image[@digest]

个人感觉这种方式不太好

```
docker run -d -p 82:80 nginx@sha256:7250923ba3543110040462388756ef099331822c6172a050b12c7a38361ea46f nginx
```

@digest在仓库的对应镜像中找，比如docker hub中

![image-20211104182926548](https://could-res-1252778021.file.myqcloud.com/img/20211104182926.png)

