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





















