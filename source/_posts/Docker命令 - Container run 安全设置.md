---
title: Docker命令 - Container run 安全设置
abbrlink: 7c35321e
date: 2021-11-17 15:04:44
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/160a5ee1ae70b57e5aae7ce3e101e094.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/160a5ee1ae70b57e5aae7ce3e101e094.jpg
---









# 安全设置?

| 选项                                    | 说明                             |
| --------------------------------------- | -------------------------------- |
| --security-opt="label=user:USER"        | 为容器设置标签用户               |
| --security-opt="label=role:ROLE"        | 为容器设置标签角色               |
| --security-opt="label=type:TYPE"        | 设置容器的标签类型               |
| --security-opt="label=level:LEVEL"      | 设置容器的标签级别               |
| --security-opt="label=disable"          | 关闭容器的标签限制               |
| --security-opt="apparmor=PROFILE"       | 设置容器要应用的访问控制系统文件 |
| --security-opt="no-new-privileges:true" | 禁止容器进程授权                 |
| --security-opt="seccomp=unconfined"     | 关闭容器的seccomp限制            |
| --security-opt="seccomp=profile.json"   |                                  |

