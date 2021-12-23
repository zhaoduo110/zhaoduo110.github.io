---
title: Golang编译各系统可执行文件
abbrlink: cc041e36
date: 2021-12-23 13:48:17
tags:
categories:
  - Go
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/timg1.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/timg1.jpg
---



GOOS：     目标可执行程序运行操作系统，支持 `darwin，freebsd，linux，windows

GOARCH：目标可执行程序操作系统构架，包括`386，amd64，arm



编译linux可执行文件

```
SET CGO_ENABLED=0
SET GOOS=linux
SET GOARCH=amd64
```

编译window可执行文件

```
SET CGO_ENABLED=0
SET GOOS=windows
SET GOARCH=amd64
```

编译Mac可执行文件

```
SET CGO_ENABLED=0
SET GOOS=darwin3 
SET GOARCH=amd64
```



然后执行 `go build xxx.go` 即可
