---
title: Docker安装
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/80cb39dbb6fd526601e80c62ad18972bd5073680.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/80cb39dbb6fd526601e80c62ad18972bd5073680.jpg
abbrlink: 2a40ac23
date: 2021-10-27 17:14:07
---



# Centos

## 首先卸载旧版本Docker

```bash
yum remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine
```



## 安装方法

有一下几种不同方式可以安装 Docker Engine

- 使用 Docker 的存储库，这种方式便于安装和升级服务（推荐）
- RPM 包安装
- 自动化脚本安装



## 使用存储库安装

### 设置存储库

```
yum install -y yum-utils
yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

启用/禁用发布渠道

`--enable表示启用  --disable表示禁用`

```
yum-config-manager --enable docker-ce-nightly
yum-config-manager --enable docker-ce-test
```

#### 关于发布渠道的官方说明

>## 发布渠道
>
>Docker Engine 有 3 种更新渠道，**stable**、**test**和**nightly**：
>
>- 在**稳定**频道为您提供全面上市的最新版本。
>- 该**测试**通道提供了预发布的，可用于一般的前可用性（GA）测试。
>- 在**每晚**频道为您提供最新的构建工作，为下一个主要版本进展。
>
>### 稳定
>
>年月发布是从与主分支不同的发布分支发布的。分支是用格式创建的`<year>.<month>`，例如 `20.10`。年月名称表示预计该版本将普遍可用的最早可能日历月。所有进一步的补丁发布都是从该分支执行的。例如，一旦`v20.10.0`发布，所有后续补丁版本都是从该`20.10`分支构建的。
>
>### 测试
>
>在准备新的年月发布时，`YY.mm`当 Docker 为发布所需的里程碑已实现功能完整时，会从 master 分支创建一个具有格式的分支。Beta 版和候选版等预发布是从各自的发布分支进行的。补丁发布和相应的预发布是从相应的发布分支内执行的。
>
>### 每晚
>
>每晚构建为您提供下一个主要版本正在进行的最新构建。它们每天从 master 分支创建一次，版本格式为：
>
>```
>0.0.0-YYYYmmddHHMMSS-abcdefabcdef
>```
>
>其中 time 是 UTC 中的提交时间，最后一个后缀是提交哈希的前缀，例如`0.0.0-20180720214833-f61e0f7`.
>
>这些构建允许从主分支上的最新代码进行测试。对于每晚构建没有任何资格或保证



### 安装Docker引擎

#### 安装最新版本

```
yum install docker-ce docker-ce-cli containerd.io
```

- 如果启用了多个 Docker 存储库，则在`yum install`或 `yum update`命令中未指定版本的情况下安装或更新始终会安装可能的最高版本，这可能不够稳定。

- 此命令会安装 Docker，但不会启动 Docker。会创建一个 `docker`组，但是，默认情况下不会向该组添加任何用户



#### 安装特定版本

首先查看版本列表

```
yum list docker-ce --showduplicates | sort -r
```

进行安装

```
yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

VERSION_STRING：下图中第二个红框的内容

![image-20211027180945561](https://could-res-1252778021.file.myqcloud.com/img/image-20211027180945561.png)



### 启动Docker

```
systemctl start docker
```

### 验证Docker是否安装成功

```
docker run hello-world
```



### 升级Docker

按照 安装特定版本 的方式操作即可



### Docker命令

```
# 查看docker当前版本信息
docker --version
```



## 卸载Docker引擎

### 卸载 Docker Engine、CLI 和 Containerd 包

```
yum remove docker-ce docker-ce-cli containerd.io
```

### 删除所有镜像、容器和卷

```
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```

==已编辑的配置文件需要手动删除==





## image仓库的镜像网址

由于人尽皆知的原因，国内访问Docker的官方仓库很慢，所以可以通过以下方式把仓库网址改为国内的镜像站











# Windows(10)

## 安装Docker DeskTop

### 开启Hyper-V

注意：windows上安装docker需要开启Hyper-v功能（开启此功能好像会导致VMWare启动不了）

这里搜索的时候只能用拼音qiyong，试了好几次中文搜不出来

![image-20211028191539463](https://could-res-1252778021.file.myqcloud.com/img/image-20211028191539463.png)

找到Hyper-V 前面复选框选上，点击确定，等待安装完成重启就可以了

![image-20211028191640598](https://could-res-1252778021.file.myqcloud.com/img/image-20211028191640598.png)



##### 修改虚拟机和虚拟硬盘存储位置

![image-20211028191136178](https://could-res-1252778021.file.myqcloud.com/img/image-20211028191136178.png)







### 安装

直接下载安装包即可：https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe



### 配置修改

因为docker默认安装到C盘，很多（比如images）的存储位置也是在C盘，为了节省C盘空间，所以需要把一些存储文件存到其他地方

#### images存储位置修改

![image-20211028190813323](https://could-res-1252778021.file.myqcloud.com/img/image-20211028190813323.png)



#### Hyper-V虚拟硬盘

##### 打开Hyper-V管理器

![image-20211028191037421](https://could-res-1252778021.file.myqcloud.com/img/image-20211028191037421.png)





## 卸载

参考：https://www.cnblogs.com/lonquanzj/p/8911977.html

### 卸载docker程序

新建文件 `a.ps1`

```
$ErrorActionPreference = "SilentlyContinue"
 
kill -force -processname 'Docker for Windows', com.docker.db, vpnkit, com.docker.proxy, com.docker.9pdb, moby-diag-dl, dockerd
 
try {
    ./MobyLinux.ps1 -Destroy
} Catch {}
 
$service = Get-WmiObject -Class Win32_Service -Filter "Name='com.docker.service'"
if ($service) { $service.StopService() }
if ($service) { $service.Delete() }
Start-Sleep -s 5
Remove-Item -Recurse -Force "~/AppData/Local/Docker"
Remove-Item -Recurse -Force "~/AppData/Roaming/Docker"
if (Test-Path "C:\ProgramData\Docker") { takeown.exe /F "C:\ProgramData\Docker" /R /A /D Y }
if (Test-Path "C:\ProgramData\Docker") { icacls "C:\ProgramData\Docker\" /T /C /grant Administrators:F }
Remove-Item -Recurse -Force "C:\ProgramData\Docker"
Remove-Item -Recurse -Force "C:\Program Files\Docker"
Remove-Item -Recurse -Force "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Docker"
Remove-Item -Force "C:\Users\Public\Desktop\Docker for Windows.lnk"
Get-ChildItem HKLM:\software\microsoft\windows\currentversion\uninstall | % {Get-ItemProperty $_.PSPath}  | ? { $_.DisplayName -eq "Docker" } | Remove-Item -Recurse -Force
Get-ChildItem HKLM:\software\classes\installer\products | % {Get-ItemProperty $_.pspath} | ? { $_.ProductName -eq "Docker" } | Remove-Item -Recurse -Force
Get-Item 'HKLM:\software\Docker Inc.' | Remove-Item -Recurse -Force
Get-ItemProperty HKCU:\software\microsoft\windows\currentversion\Run -name "Docker for Windows" | Remove-Item -Recurse -Force
#Get-ItemProperty HKCU:\software\microsoft\windows\currentversion\UFH\SHC | ForEach-Object {Get-ItemProperty $_.PSPath} | Where-Object { $_.ToString().Contains("Docker for Windows.exe") } | Remove-Item -Recurse -Force $_.PSPath
#Get-ItemProperty HKCU:\software\microsoft\windows\currentversion\UFH\SHC | Where-Object { $(Get-ItemPropertyValue $_) -Contains "Docker" }
```

找到”powershell“， 选择以管理员运行powershell

然后运行脚本

如果遇到报错(我在执行过程中为遇到)，如下图

![img](https://could-res-1252778021.file.myqcloud.com/img/840769-20180423005548012-470712866.png)

则执行 `set-executionpolicy remotesigned`













