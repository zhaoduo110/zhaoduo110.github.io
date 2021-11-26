---
title: Docker命令 - Container run 网络设置
abbrlink: 71fe27df
date: 2021-11-17 14:53:15
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/b1d6a559c5270e828e1049b995fa02bc-1000.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/b1d6a559c5270e828e1049b995fa02bc-1000.jpg
---



# 网络设置

### DNS设置

>  --dns=[]：为容器自定义设置dns服务器

### 网络模式

>  --network="bridge"：将容器连接到网络

等于 --net

支持的值

#### 桥接模式

> --network bridge

默认值。创建的容器默认会连接到`docker的网桥上（即docker0）`，通过docker网桥及iptables nat表配置与宿主机进行通信

#### 禁用网络

> --network none

**示例：**

以下命令启动的容器 宿主机上（包括其他网络上）nginx访问不了

```bash
docker run -p 80:80 --rm -d --network none --name nginx1 nginx:1.21
```

#### 容器共享网络连接

> --network container:<name|id>

`指定新创建的容器与已存在的容器共享网络名称空间(通过名称或id指定)`。新创建的容器不会创建网卡，而是`与已存在的容器共享一个网卡、IP等，两个容器之间可以通过lo回环网卡设备进行通信，但是其他命名空间，比如用户、文件系统等还是和容器隔离开的。`

`注意：在这种网络模式下是不允许再次使用 -p 选项来做端口映射的,所以如果需要的话在创建第一个容器的时候把该映射的端口都做映射`

**示例：**

先运行容器 nginx1

```bash
docker run -p 80:80 -p 81:81 --rm -it --name nginx1 nginx:1.21 /bin/bash
# 然后启动nginx，这里看到是可以正常启动的
nginx
# 这时候访问 宿主机的 80端口 是能正常解析的，但是81不行
```

再运行容器 nginx2  指定网络类型为 container并绑定到nginx1上

```bash
docker run --rm -it --network container:nginx1 --name nginx2 nginx:1.21 /bin/bash
# 这时候如果同样启动nginx 会发现根本运行不了
# nginx报错 bind() to 0.0.0.0:80 failed (98: Address already in use)

# 把nginx的默认端口改为81
apt-get update
apt-get install vim
vim /etc/nginx/conf.d/default.conf

# 再次运行 nginx
nginx
# 这时候nginx就启动成功了 而且访问宿主机的81端口也是能正常解析的 因为81端口已经在nginx1的时候已经做过映射了
```

以上这个小实验说明了container的网络模式怎么使用和对应的效果

#### 主机模式

> --network host

与container模式有些类似，container模式是容器和容器之间共享网络名称空间，host模式下docker不会为容器创建网络名称空间而是与宿主机共用一个网络名称空间，并`使用宿主机的网卡、端口等信息，但是其他名称空间，比如用户、文件系统等还是和宿主机隔离开的`

我们在宿主机上通过host模式启动一个nginx容器，并监听80端口，此时在容器内部通过ifconfig或ip命令查看网络环境时，所看到的信息和宿主机的信息一致，就好比nginx直接运行在宿主机一样，但是，用户、文件系统等还是和宿主机隔离开的

`注意：这种网络模式下也是不可以使用 -p 或者-P 来做端口映射的，因为本身容器用的就是宿主机的网络`

**示例：**

```bash
docker run --rm -it --network host --name nginx1 nginx:1.21 /bin/bash
```

`都说是这么用，但是总感觉不太对，实际的容器网络信息和宿主机的不太一样，不确定是不是因为宿主机是windows的原因`

#### 自建网络模式

> --network="<network-name>|<network-id>"

将容器连接到用户（使用docker network create命令）创建的网络上。使用这种方式将多个容器加入到同一个网络上，可以轻松实现容器间的网络通信，在一个容器中使用另一个容器的IP地址或名称进行通信

**示例：**

查看当前已有的网络

```bash
docker network ls
```

创建网络（创建网络的更多使用方法会有专门章节进行说明）

```bash
docker network create -d bridge my-net
```

运行容器时指定容器网络为自建的网络

```bash
docker run --rm -it --network my-net --name nginx1 nginx:1.21 /bin/bash
```



### 网络别名

> --network-alias：为容器设置网络别名

**示例：**

先启动一个nginx容器 网络别名 nginx1  容器名称nginx11

```bash
docker run --rm -it --network my-net --network-alias nginx1 --name nginx11 nginx:1.21 /bin/bash
```

再启动一个centos容器，在此容器内ping nginx1和nginx11 会发现都是通的

```bash
docker run -it -p 22:22 --network="my-net" --name=centos1 centos /bin/bash

[root@425bb4a11394 /]# ping nginx1
PING nginx1 (172.18.0.3) 56(84) bytes of data.
64 bytes from nginx11.my-net (172.18.0.3): icmp_seq=1 ttl=64 time=0.064 ms
64 bytes from nginx11.my-net (172.18.0.3): icmp_seq=2 ttl=64 time=0.113 ms
^C
--- nginx1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1006ms
rtt min/avg/max/mdev = 0.064/0.088/0.113/0.026 ms

[root@425bb4a11394 /]# ping nginx11
PING nginx11 (172.18.0.3) 56(84) bytes of data.
64 bytes from nginx11.my-net (172.18.0.3): icmp_seq=1 ttl=64 time=0.059 ms
64 bytes from nginx11.my-net (172.18.0.3): icmp_seq=2 ttl=64 time=0.052 ms
^C
--- nginx11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1018ms
rtt min/avg/max/mdev = 0.052/0.055/0.059/0.008 ms
```



### 添加hosts内容

> --add-host 源地址:目标ip

管理/etc/hosts，向/etc/hosts中添加行

`注意：`

如果一个容器连接到默认的网桥网络并与其他容器链接（[容器共享网络连接模式](#容器共享网络连接)），那么容器的/etc/hosts文件将被更新为链接的容器的名称

由于Docker可能会实时更新容器的/etc/hosts文件，因此可能会出现容器内部进程读取空的或不完整的/etc/hosts文件的情况。一般再次尝试读取应该可以解决这个问题。

**示例：**

首先从本地拿到百度的网站ip

```bash
C:\Users\zhaoduo>ping baidu.com

正在 Ping baidu.com [220.181.38.251] 具有 32 字节的数据:
来自 220.181.38.251 的回复: 字节=32 时间=28ms TTL=47
来自 220.181.38.251 的回复: 字节=32 时间=27ms TTL=47

220.181.38.251 的 Ping 统计信息:
    数据包: 已发送 = 2，已接收 = 2，丢失 = 0 (0% 丢失)，
往返行程的估计时间(以毫秒为单位):
    最短 = 27ms，最长 = 28ms，平均 = 27ms
```

启动一个centos容器并向hosts文件内加一个虚拟域名指向到百度的网站ip上

```bash
docker run -it --rm --name=centos2 --add-host local.baidu.com:220.181.38.251 centos /bin/bash
```

在容器内测试hosts解析正常

```bash
[root@2bcb5883b5ae /]# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
220.181.38.251  local.baidu.com
172.17.0.3      2bcb5883b5ae

[root@2bcb5883b5ae /]# ping local.baidu.com
PING local.baidu.com (220.181.38.251) 56(84) bytes of data.
64 bytes from local.baidu.com (220.181.38.251): icmp_seq=1 ttl=37 time=30.3 ms
64 bytes from local.baidu.com (220.181.38.251): icmp_seq=2 ttl=37 time=31.5 ms
64 bytes from local.baidu.com (220.181.38.251): icmp_seq=3 ttl=37 time=29.10 ms
^C
--- local.baidu.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 29.978/30.579/31.494/0.672 ms

[root@2bcb5883b5ae /]# wget local.baidu.com
--2021-11-06 06:31:01--  http://local.baidu.com/
Resolving local.baidu.com (local.baidu.com)... 220.181.38.251
Connecting to local.baidu.com (local.baidu.com)|220.181.38.251|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 81 [text/html]
Saving to: 'index.html'

index.html                                                  100%[========================================================================================>]      81  --.-KB/s    in 0s

2021-11-06 06:31:01 (14.3 MB/s) - 'index.html' saved [81/81]
```



