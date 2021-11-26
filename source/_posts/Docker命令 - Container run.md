---
title: Docker命令 - Container run
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1580638993-thbUPVEsRf.jpeg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1580638993-thbUPVEsRf.jpeg
abbrlink: b1109de1
date: 2021-10-29 18:26:11
---







# 基本说明

docker指令官方文档：https://docs.docker.com/reference/

docker run官方文档：https://docs.docker.com/engine/reference/commandline/container_run/

基本语法

```
docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]
```

必须要指定镜像

OPTIONS

| 名称，缩写                   | 默认    | 描述说明                                                     |
| ---------------------------- | ------- | ------------------------------------------------------------ |
| --add-host                   |         | host:ip。添加自定义 host-to-ip 映射                          |
| --attach， -a                |         | 附加到STDIN、STDOUT或STDERR                                  |
| --blkio-weight               |         | 所有的容器对于 IO 操作「block IO bandwidth – blkio」都拥有相同优先级。<br>可以通过 `--blkio-weight` 修改容器 blkio 权重，权重值在10和1000之间，或0禁用(默认0) |
| --blkio-weight-device        |         | 指定某个设备的权重大小，如果同时指定 --blkio-weight 则以 --blkio-weight 为全局默认配置，<br>针对指定设备以 --blkio-weight-device 指定设备值为主 |
| --cap-add                    |         | 添加Linux功能权限（指定某个）                                |
| --cap-drop                   |         | 关闭Linux功能权限（指定某个）                                |
| --cgroup-parent              |         | 设置用于容器的默认cgroup父级                                 |
| --cgroupns                   |         | API 1.41+ <br>要使用的Cgroup命名空间(host\|private)<br>'host'：在Docker主机的cgroup命名空间中运行容器<br>'private'：在自己的cgroup命名空间中运行容器<br>''：使用cgroup命名空间，由守护进程的default-cgroupns-mode选项配置(默认) |
| --cidfile                    |         | 将容器ID写入文件                                             |
| --cpu-count                  |         | CPU核数（仅限windows）                                       |
| --cpu-percent                |         | CPU百分比（仅限windows）                                     |
| --cpu-period                 |         | 限制CPU CFS(完全公平调度程序)周期                            |
| --cpu-quota                  |         | 限制CPU CFS(完全公平调度程序)配额                            |
| --cpu-rt-period              |         | API 1.25+<br>限制CPU real-time 周期(以微秒为单位)            |
| --cpu-rt-runtime             |         | API 1.25+<br/>限制CPU real-time 运行时间(以微秒为单位)       |
| --cpu-shares， -c            |         | CPU份额(相对权重)                                            |
| --cpus                       |         | API 1.25+<br/>可获取CPU个数                                  |
| --cpuset-cpus                |         | 允许执行的cpu (0- 3,0,1)                                     |
| --cpuset-mems                |         | 允许执行的内存 (0- 3,0,1)                                    |
| --detach，-d                 |         | 在后台运行container并打印容器ID                              |
| --detach-keys                |         | 重写分离容器的键序列                                         |
| --device                     |         | 向容器添加主机设备                                           |
| --device-cgroup-rule         |         | 在cgroup允许的设备列表中添加一条规则                         |
| --device-read-bps            |         | 限制从设备读取速率(bytes / s)                                |
| --device-read-iops           |         | 限制设备的读速率(IO / s)                                     |
| --device-write-bps           |         | 限制设备的写入速率(bytes / s)                                |
| --device-write-iops          |         | 限制设备的写速率(IO / s)                                     |
| --disable-content-trust      | true    | 跳过image验证                                                |
| --dns                        |         | 设置容器的dns服务器                                          |
| --dns-opt                    |         | 设置DNS选项                                                  |
| --dns-option                 |         | 设置DNS选项                                                  |
| --dns-search                 |         | 指定容器DNS搜索域名                                          |
| --domainname                 |         | 容器NIS域名                                                  |
| --entrypoint                 |         | 覆盖镜像默认的ENTRYPOINT                                     |
| --env，-e                    |         | 设置一个环境变量                                             |
| --env-file                   |         | 读入包含环境变量的文件                                       |
| --expose                     |         | 公开端口或端口范围                                           |
| --gpus                       |         | API 1.40+<br>要添加到容器中的GPU设备<br>'all' ：以通过所有GPU |
| --group-add                  |         | 添加要加入的附加组                                           |
| --health-cmd                 |         | 用于检查健康状态的命令                                       |
| --health-interval            |         | 运行检查健康状态命令的时间间隔(ms \| s \| m\| h) （default 0s） |
| --health-retries             |         | 报告不正常状态的连续失败次数                                 |
| --health-start-period        |         | API 1.29+<br>容器初始运行情况的启动周期。<br>意思大概是设置了这个时间，容器启动时在这个时间后开始 health-retries |
| --health-timeout             |         | 一次检查运行的最长时间(ms \| s \| m \| h) （default 0s）     |
| --help                       |         |                                                              |
| --hostname，-h               |         | 设置容器的hostname                                           |
| --init                       |         | API 1.25+<br>在容器中运行一个init来转发信号和获取进程        |
| --interactive，-i            |         | 即使没有连接，也保持STDIN打开                                |
| --io-maxbandwidth            |         | 系统驱动器的最大IO带宽限制(仅限Windows)                      |
| --io-maxiops                 |         | 系统驱动器的最大IOps限制(仅限Windows)                        |
| --ip                         |         | IPv4地址(如172.30.100.104)                                   |
| --ip6                        |         | IPv6 地址 (如 2001:db8::33)                                  |
| --ipc                        |         | 使用IPC模式                                                  |
| --isolation                  |         | 容器隔离技术                                                 |
| --kernel-memory              |         | 内核内存限制                                                 |
| --label，-l                  |         | 设置容器上的元数据                                           |
| --label-file                 |         | 读入以行分隔的标签文件                                       |
| --link                       |         | 添加到另一个容器的链接                                       |
| --link-local-ip              |         | 容器IPv4/IPv6链路本地地址                                    |
| --log-driver                 |         | 容器的日志驱动程序                                           |
| --log-opt                    |         | 日志驱动器选项                                               |
| --mac-address                |         | 容器MAC地址(例如，92:d0:c6:0a:29:33)                         |
| --memory，-m                 |         | 内存限制                                                     |
| --memory-reservation         |         | 内存软限制                                                   |
| --memory-swap                |         | 交换区限制 = 内存+交换区大小<br>-1 不限制                    |
| --memory-swappiness          | -1      | 调整容器虚拟运行内存(0到100)                                 |
| --mount                      |         | 将文件系统挂载到容器                                         |
| --name                       |         | 为容器指定一个名称                                           |
| --net，--network             |         | 指定容器连接的网络 ，建议使用--network，最新更新的<br>官方解释--network是更好的，但是--net也不会删除 |
| --net-alias，--network-alias |         | 为容器添加网络别名，建议使用--network-alias                  |
| --no-healthcheck             |         | 禁用任何容器指定的HEALTHCHECK                                |
| --oom-kill-disable           |         | 禁用OOM Killer                                               |
| --oom-score-adj              |         | 优化主机的OOM参数(-1000到1000)                               |
| --pid                        |         | 要使用的PID命名空间                                          |
| --pids-limit                 |         | 调整容器pid限制(设置-1为无限)                                |
| --platform                   |         | API 1.32+<br>如果服务器支持多平台，则设置平台                |
| --privileged                 |         | 将扩展权限授予该容器                                         |
| --publish，-p                |         | 主机端口与容器端口映射（主机端口:容器端口）                  |
| --publish-all，-P            |         | 将容器暴露所有的端口映射给主机随机端口                       |
| --pull                       | missing | 在容器运行之前拉取镜像("always"\|"missing" \| "never")       |
| --read-only                  | no      | 将容器的根文件系统挂载为只读                                 |
| --restart                    |         | 在容器退出时应用重新启动策略                                 |
| --rm                         |         | 当容器退出时自动删除容器，删除时未指定名称的挂载卷也会被删除，指定了名称的不会删除 |
| --runtime                    |         | 容器运行时                                                   |
| --security-opt               |         | 安全设置                                                     |
| --shm-size                   |         | /dev/shm的大小                                               |
| --sig-proxy                  | true    | 为进程代理接收信号                                           |
| --stop-signal                | SIGTERM | 停止容器的信号                                               |
| --stop-timeout               |         | API 1.25+<br>停止容器的超时时间(以秒为单位)                  |
| --storage-opt                |         | 容器的存储驱动程序选项                                       |
| --sysctl                     |         | Sysctl选项                                                   |
| --tmpfs                      |         | 挂载tmpfs目录                                                |
| --tty，-t                    |         | 分配一个pseudo-TTY                                           |
| --ulimit                     |         | Ulimit选项                                                   |
| --user，-u                   |         | 用户名或UID（<name\|uid>[:<group\|gid>]）                    |
| --userns                     |         | 要使用的用户命名空间                                         |
| --uts                        |         | 要使用的UTS 命名空间                                         |
| --volume，-v                 |         | 绑定挂载卷                                                   |
| --volume-driver              |         | 容器的可选卷驱动程序                                         |
| --volumes-from               |         | 从指定容器装入卷                                             |
| --workdir，-w                |         | 容器内的工作目录                                             |

里面的很多命令不懂不知道怎么用？ 

没得关系，其实我也不懂，hhhhh\~\~\~        下面会陆续补充各种相关场景的命令使用



时空之门，当当当当 ~ ~ ~ ~ ~ ~ ~  ~ ~ ~ ~ ~

- {% post_link 'Docker命令 - Container run 运行模式' %}
- {% post_link 'Docker命令 - Container run 标识' %}
- {% post_link 'Docker命令 - Container run 重启策略' %}
- {% post_link 'Docker命令 - Container run 网络设置' %}
- {% post_link 'Docker命令 - Container run 资源限制' %} - 待完善
- {% post_link 'Docker命令 - Container run 安全设置' %} - 待完善











# 指定初始化进程?

> --init 







# 指定自定义的cgroups?

> --cgroup-parent



















# IMAGE相关

|                                                            |                                 |
| ---------------------------------------------------------- | ------------------------------- |
| docker image ls                                            | 列出本机的所有 image 文件       |
| docker image rm [imageName / imageId]                      | 删除 image 文件                 |
| docker image pull library/hello-world                      | 拉取仓库镜像到本地              |
| docker image build -t [username]/[repository]:[tag] .      |                                 |
| docker image tag [imageName] [username]/[repository]:[tag] | 为本地的 image 标注用户名和版本 |
| docker image push [username]/[repository]:[tag]            | 发布 image 文件                 |
|                                                            |                                 |
|                                                            |                                 |
|                                                            |                                 |
|                                                            |                                 |
|                                                            |                                 |







# CONTAINER相关

|                                                   |                                                              |
| ------------------------------------------------- | ------------------------------------------------------------ |
| docker container ls                               |                                                              |
| docker container ls --all                         | docker container ls -a                                       |
|                                                   |                                                              |
| docker container run                              | 运行容器（每次运行会生成一个新的container）                  |
| docker container start                            | 启动已经生成、已经停止运行的容器                             |
| docker container kill [containID]                 | 终止容器运行（向容器里面的主进程发出 SIGKILL 信号）          |
| docker container stop                             | 终止容器运行（向容器里面的主进程发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号） |
| docker container rm [containerID]                 | 删除容器                                                     |
| docker container logs                             | 查看 docker 容器的输出，即容器里面 Shell 的标准输出（如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要用这个命令查看输出） |
| docker container exec                             | 进入一个正在运行的 docker 容器。（如果`docker run`命令运行容器的时候，没有使用`-it`参数，就要用这个命令进入容器） |
| docker container cp [containID]:[/path/to/file] . | 从正在运行的 Docker 容器里面，将文件拷贝到本机               |























