---
title: Docker命令 - 容器命令
abbrlink: bdf925e1
date: 2021-11-24 16:10:23
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-g71zx3.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-g71zx3.jpg
---



> docker ps

显示容器列表，等于 `docker container ls`

选项

-a, --all           显示所有容器，默认只会显示运行中的

-n, --last n      n为int值，显示最后创建的n个容器，默认-1

-q, --quiet       只显示容器id



> 退出容器

指在容器内退出

`exit` 退出。如果当前有正在运行的前台进程（包括命令行终端）则退出后不会停止容器，否则退出后容器会被停止

`Ctrl + P + Q `  退出但不停止容器

**退出状态**

非0状态都是异常退出

| 状态码 | 异常说明           |
| ------ | ------------------ |
| 125    | docker服务本身异常 |
| 126    | 不能调用容器命令   |
| 127    | 找不到容器命令     |



> docker [container] rm 容器id [容器id]

删除指定的容器，指定多个容器id用空格分割可以删除多个

-f 强制删除

以下命令可以强制删除全部容器

```shell
docker rm -f $(docker ps -aq)
# 或者
docker ps -a -q | xargs docker rm
```



>  docker [container] create 

创建容器



> docker [container] start 容器id

启动容器



> docker [container] run 

创建并启动容器，还可以直接进入容器

 选项：

--rm   当容器退出时自动删除容器，删除时未指定名称的挂载卷也会被删除，指定了名称的不会删除

注意：

`1. --rm不可以和 --restart一起使用`

`2. 如果 -d 结合 --rm 使用，则在容器停止后或者容器的主进程结束后会删除此容器`

**示例：**

```bash
docker run -it --rm --name=centos2 --add-host local.baidu.com:220.181.38.251 centos /bin/bash
```



更多使用方法见 {% post_link 'Docker命令 - Container run' %}





> docker [container] restart 容器id 

重启容器



> docker [container] stop 容器id

停止当前正在运行的容器



> docker [container] kill 容器id

强制停止当前容器



> docker [container] exec 

进入容器打开新的终端

```shell
docker exec -it 容器id /bin/bash
```



> docker [container] attach 容器id

进入容器，进入的是已经在运行的终端





> docker [container] top 容器id

查看容器的进程信息



> docker [container] logs 容器id

查看容器日志

选项

--details			             显示日志详情

-f, --follow		             跟踪日志输出

​	--since string			显示某个时间以来的日志，时间戳(如2013-01-02T13:23:37)或相对时间(如42分钟42m)

​	--tail string				从日志末尾开始显示的行数(默认为“all”)

-t, --timestamps            显示时间

​	--until string				显示时间之前的日志(例如2013-01-02T13:23:37)或相对时间(例如42m for 42 minutes)

```shell
docker logs -t --tail n 容器id #查看n行日志
docker logs -ft 容器id #跟着日志
```





> docker [container] cp 容器id:容器内路径 主机目的路径

把容器的文件拷贝到宿主机上





> docker [container] commit -m="描述信息" -a="作者" 容器id 目标镜像名:[TAG]

容器当前状态提交为本地镜像



> docker [container] logs 容器id

查看容器日志







