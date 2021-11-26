---
title: Docker命令 - Container run 命名空间
abbrlink: 5be26738
date: 2021-11-17 15:03:34
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/27bebf978311086424d2c483e282cfe2-1000.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/27bebf978311086424d2c483e282cfe2-1000.jpg
---





# 命名空间

涉及到命名空间，暂时没搞清楚，先放下，后面专门学习一下命名空间

> --pid=''：PID设置



>--uts=''：UTS设置



> --ipc="MODE"：IPC设置

MODE支持的值

| 值                            | 说明                                        |
| ----------------------------- | ------------------------------------------- |
| ''                            | 使用守护进程的系统默认值                    |
| 'none'                        | 拥有私有IPC命名空间，不挂载/dev/shm         |
| 'private'                     | 拥有私有IPC命名空间                         |
| 'shareable'                   | 拥有私有IPC命名空间，并有可能与其他容器共享 |
| 'container: <\_name-or-ID\_>' | 加入另一个 'shareable' 容器的IPC命名空间    |
| 'host'                        | 使用主机系统的IPC名称空间                   |





