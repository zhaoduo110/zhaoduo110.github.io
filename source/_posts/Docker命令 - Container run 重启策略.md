---
title: Docker命令 - Container run 重启策略
abbrlink: '65065780'
date: 2021-11-17 14:54:49
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/357c3f971c6ef56ae6c15d868ceba856.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/357c3f971c6ef56ae6c15d868ceba856.jpg
---







# 重启策略

容器退出时的重启策略

> --restart

支持的值

| 值                       | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| no                       | 默认值，容器退出时不会自动重启容器                           |
| on-failure[:max-retries] | 只有当容器非正常状态（非0状态）退出时，才重新启动。可以设置最大重启次数 |
| always                   | 在容器退出时不管容器的当前状态如何都会重启容器               |
| unless-stopped           | 无论退出状态如何，总是重新启动容器，除了Docker服务停止之前就处于停止状态的容器不会重启 |

每次重启都会增加延迟(从100毫秒开始，是之前延迟的两倍)，防止服务器崩掉。就是说守护进程需要等待100毫秒，然后是200毫秒、400、800、1600，等等，直到达到 on-failure 限制（1分钟的最大延迟），或者执行 `docker stop` 或 `docker rm -f` 容器时

如果容器成功重启(容器启动并运行至少10秒)，延迟被重置为默认值100毫秒。

查看容器重启次数

```bash
docker inspect -f "{{ .RestartCount }}" container-name
```

查看容器最近一次(重新)启动时间

```bash
docker inspect -f "{{ .State.StartedAt }}" container-name
```

`注意：--restart不可以和--rm一起使用`



## no

> --restart no

无论如何都不会进行重启

**示例：**

窗口1：启动容器nginx1

```bash
docker run -it -p 80:80 --restart no --name nginx1 nginx:1.21 /bin/bash
```

窗口2：查看当前运行的容器。此时容器是 Up状态

![image-20211106151849425](https://could-res-1252778021.file.myqcloud.com/img/20211106151849.png)

窗口1：使用exit退出容器

![image-20211106151947811](https://could-res-1252778021.file.myqcloud.com/img/20211106151947.png)

窗口2：查看当前运行的容器。此时容器是 Exited 状态。容器不会重新启动

![image-20211106152033573](https://could-res-1252778021.file.myqcloud.com/img/20211106152033.png)

实验完毕删除容器



## always

> --restart always

无论如何只要容器退出就会重启，docker stop关闭容器除外

**示例：**

窗口1：启动容器nginx1

```bash
docker run -it -p 80:80 --restart always --name nginx1 nginx:1.21 /bin/bash
```

窗口2：查看当前运行的容器。此时容器是 Up状态

![image-20211106151557938](https://could-res-1252778021.file.myqcloud.com/img/20211106151558.png)

窗口1：使用exit退出容器

![image-20211106152245630](https://could-res-1252778021.file.myqcloud.com/img/20211106152245.png)

窗口2：查看当前运行的容器。此时容器还是 Up 状态。注意看启动时间，说明是重启的  而不是一直在Up的状态

![image-20211106152323085](https://could-res-1252778021.file.myqcloud.com/img/20211106152323.png)

实验完毕删除容器



## unless-stopped

> --restart unless-stopped

**unless-stopped** 和 **always** 基本一样，只有以下区别

- 处于关闭状态的容器，docker服务重启（退出再次启动或者直接重启）后always的容器会启动，unless-stopped不会

**示例：**

启动两个容器

```bash
docker run --restart=always -itd --name centos2 centos bash
docker run --restart unless-stopped -itd --name centos3 centos bash
# 查看两个容器都在Up状态
# 然后重启一下docker再看两个容器的状态会发现centos2是处于Up状态的  而centos3并没有起来
```

实验完毕删除容器



## on-failure[:max-retries]

> --restart on-failure[:max-retries]

容器非正常状态（非0状态）退出时进行重启，并且可以设置最大重试次数

**示例：**

启动一个centos容器centos2，执行一个错误指令（会导致启动失败从而进行重启）

```bash
docker run -it --restart on-failure --name centos2 centos /bin/bash -c "qwesadfsagfasge"
```

再启动一个centos容器centos3，执行一个错误指令，并且设置重启策略为 非正常退出时重启，最多重启三次

```bash
docker run -it --restart on-failure:3 --name centos3 centos /bin/bash -c "qwesadfsagfasge"
```

过一会儿查看容器状态  会发现centos3在进行3次尝试重启之后已经处于Exited状态，而centos2还在进行重启

实验完毕删除容器

