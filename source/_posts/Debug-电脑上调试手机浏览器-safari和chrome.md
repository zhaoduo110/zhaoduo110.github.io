---
title: Debug-电脑上调试手机浏览器(safari和chrome)
categories:
  - - Debug
tags:
  - Debug
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg
abbrlink: '74840084'
date: 2021-09-16 10:39:47
---



# IOS在Windows上调试

管理员身份运行powershell

## 安装 iTunes

Windows 系统首先要安装 iTunes ，打开 Apple 官网下载 [iTunes](https://www.apple.com/cn/itunes/download/) 并完成 iTunes 安装，否则计算机无法正确识别 iPhone 设备

## 手机设置

手机通过数据线连接电脑

设置-->sarafi浏览器-->高级-->网页检查器打开

## 安装scoop

```
set-executionpolicy remotesigned -scope currentuser
iex (new-object net.webclient).downloadstring('https://get.scoop.sh')
```

验证scoop安装成功

```
scoop help
```

scoop包括软件的安装目录在 User\用户\scoop，如果安装失败则需要删除对应app目录再次执行安装

```
scoop bucket add extras
```

## 为scoop加速

```
scoop install aria2
```

## 安装 ios-webkit-debug-proxy

```
scoop install ios-webkit-debug-proxy
```

## 在终端启动 proxy

```
ios_webkit_debug_proxy -f chrome-devtools://devtools/bundled/inspector.html
```

启动成功

![image-20210617163415348](https://could-res-1252778021.file.myqcloud.com/img/image-20210617163415348.png)

## pc浏览器

chrome访问 `chrome://inspect/#devices`

可以进行相应设置

访问 `http://localhost:9221/` 即可看到连接的设备

访问 `http://localhost:9222/` 即可看到可检查的页面

调试信息`devtools://devtools/bundled/inspector.html?ws=localhost:9222/devtools`

