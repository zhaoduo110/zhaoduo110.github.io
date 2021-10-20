---
title: ClickHouse-安装与使用
abbrlink: '43533876'
date: 2021-04-12 14:46:42
categories:
  - - SQL
tags:
  - ClickHouse
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/5a27d7aa3c85b.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/5a27d7aa3c85b.jpg'
---



## 安装

### rpm包安装

添加官方存储库

```
yum install -y yum-utils
rpm --import https://repo.clickhouse.tech/CLICKHOUSE-KEY.GPG
yum-config-manager --add-repo https://repo.clickhouse.tech/rpm/stable/x86_64
```

如果想使用最新的版本，最后一个命令中的stable用 testing替代(只推荐用于测试环境)。`prestable`有时也可用

进行安装

```
yum install -y clickhouse-server clickhouse-client
```

设为开启启动

```
systemctl enable clickhouse-server
```

启动/重启/关闭clickhouse

```
systemctl start clickhouse-server
systemctl status clickhouse-server	# 运行状态 可以看到配置文件和pid文件地址等信息
systemctl restart clickhouse-server
systemctl stop clickhouse-server
```

clickhouse命令

```
clickhouse local [args] 
clickhouse client [args] 
clickhouse benchmark [args] 
clickhouse server [args] 
clickhouse extract-from-config [args] 
clickhouse compressor [args] 
clickhouse format [args] 
clickhouse copier [args] 
clickhouse obfuscator [args] 
clickhouse git-import [args] 
clickhouse install [args] 
clickhouse start [args] 
clickhouse stop [args] 
clickhouse status [args] 
clickhouse restart [args] 
clickhouse hash-binary [args]
```



