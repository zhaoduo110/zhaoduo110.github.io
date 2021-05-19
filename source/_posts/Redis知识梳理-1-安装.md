---
title: Redis知识梳理-1-安装
abbrlink: 97c16208
date: 2021-05-17 22:05:28
categories:
  - - NoSQL
tags:
  - Redis
  - NoSQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg'
---





# 概述

>   Redis：（Remote Dictionary Server ） 远程字典服务
>
>   是一个开源的使用C语言编写，支持网络，可基于内存也可持久化的日志型Key-Value NoSQL数据库，并提供多种开发语言的API

### 性能

官方测试结果：读 110000次/s ，写 81000次/s

### 网站

- 官网：https://redis.io/
- 中文网：http://redis.cn/



# 安装

## Windows

- **下载地址：**[https://github.com/MSOpenTech/redis/releases](https://github.com/MSOpenTech/redis/releases)。

1. Redis 支持 32 位和 64 位。这个需要根据你系统平台的实际情况选择，这里我们下载 **Redis-x64-xxx.zip**压缩包到 C 盘，解压后，将文件夹重新命名为 **redis**。

2. 打开一个 **cmd** 窗口  使用cd命令切换目录到 `C:\redis`  运行 `redis-server.exe redis.windows.conf`

   ```
	redis-server.exe redis.windows.conf
   ```

3. redis作为windows服务启动方式

   ```
	redis-server --service-install redis.windows.conf
   ```

4. dos命令

   ```
	启动服务：redis-server --service-start
	
	停止服务：redis-server --service-stop
   ```

5. 如果想方便的话，可以把 redis 的路径加到系统的环境变量里，这样就省得再输路径了，后面的那个 redis.windows.conf 可以省略，如果省略，会启用默认的。





---





## Linux 下安装

-   下载地址：[http://redis.io/download](http://redis.io/download)，下载最新文档版本。

### 下载安装

1. 下载、解压、编译

   ```
   wget wget http://download.redis.io/releases/redis-6.2.0.tar.gz
   tar xzf redis-6.2.0.tar.gz
   cd redis-6.2.0
   make && make install
   ```


2. make完后 redis-6.2.0目录下会出现编译后的redis服务程序redis-server,还有用于测试的客户端程序redis-cli,两个程序位于安装目录 src 目录下：

3. 启动、关闭redis服务

   ```
   cd src
   ./redis-server		#启动
   ./redis-cli -p 6379 shutdown		#关闭
   ```

4. **注意**

   ==这种方式启动redis 使用的是默认配置。也可以通过启动参数告诉redis使用指定配置文件使用下面命令启动== 

   ```
   cd src
   ./redis-server redis.conf     redis.conf是一个默认的配置文件。我们可以根据需要使用自己的配置文件。
   ```

5. 启动redis服务进程后,就可以使用测试客户端程序redis-cli和redis服务交互。比如：

   ```
   cd src
   ./redis-cli
   
   redis> set foo bar
   OK
   redis> get foo
   "bar"
   ```

### Redis支持后台运行并作为系统服务开机启动

1.   在 /etc/目录下创建 redis目录,把配置文件复制到该目录下

```
cd /etc
mkdir redis
cp /usr/local/src/redis-6.2.0/redis.conf /etc/redis/6379.conf
```

2.  将redis服务作为守护进程来运行，修改配置文件

```
vi 6379.conf

daemonize	表示是否作为守护进程运行,默认为no,改为yes
pidfile   	当服务以守护进程方式运行时,redis默认会把pid写入这个路径文件中，服务运行中该文件会存在,服务一旦停止该文件就自动删除,可以根据这个文件来判断redis是否正在运行,可不修改,默认即可,
       
修改成功之后保存退出
```
3.  管理启动、关闭、重启、查看状态的脚本

==redis源码里其实已经提供了一个初始化脚本,在安装目录下的utils里面有个**redis_init_script**即是。==

4. 复制redis_init_script脚本到/etc/init.d/redisd

    ```
    cp /usr/local/src/redis-6.2.08/utils/redis_init_script /etc/init.d/redisd
    ```

5. 对该脚本进行修改

    ```
    vim /etc/init.d/redisd
    ```

    添加一句    # chkconfig: 2345 90 10

    分别修改EXEC(redis-server的路径)、CLIEXEC(redis-cli的路径)、CLIEXEC(守护进行方式的pid所在文件路径,要和配置文件中的pidfile一致)、CONF(刚才复制的配置文件路径)的值

 ```
#!/bin/sh#
# chkconfig: 2345 90 10 

REDISPORT=6379
EXEC=/usr/local/src/redis-6.2.0/src/redis-server
CLIEXEC=/usr/local/src/redis-6.2.0/src/redis-cli

CLIEXEC=/var/run/redis_${REDISPORT}.pid
CONF="/etc/redis/${REDISPORT}.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac
 ```

5. 在/etc/init.d下的脚本都是可以在系统启动是自动启动的服务,配置redis随系统启动

   ```
   chkconfig redisd on
   ```

7. 操作命令

   ```
   > service redisd start
   > service redisd stop
   > service redisd status
   > service redisd restart
   
   centos7以上
   > systemctl start redisd
   > systemctl stop redisd
   > systemctl status redisd
   > systemctl restart redisd
   
   ```







---





## Ubuntu 下安装

1. 在 Ubuntu 系统安装 Redi 可以使用以下命令:

   ```
   $sudo apt-get update
   $sudo apt-get install redis-server
   ```

2. 启动 Redis

   ```
   redis-server
   ```


3. 查看 redis 是否启动？

   ```
   redis-cli
   ```


4. 以上命令将打开以下终端：

   ```
   redis 127.0.0.1:6379>
   ```


5. 127.0.0.1 是本机 IP ，6379 是 redis 服务端口。现在我们输入 PING 命令。

   ```
   redis 127.0.0.1:6379> ping
   PONG
   ```









# 压力测试

redis-benchmark 官方自带的性能测试工具

官方文档：http://redis.cn/topics/benchmarks.html

支持的参数

| 参数   | 含义          | 原文说明                                                     | 中文说明                                     |
| ------ | ------------- | ------------------------------------------------------------ | -------------------------------------------- |
| -h     | <hostname>    | Server hostname (default 127.0.0.1)                          | 指定服务器主机名                             |
| -p     | <port>        | Server port (default 6379)                                   | 指定服务器端口                               |
| -s     | <socket>      | Server socket (overrides host and port)                      | 指定服务器socket                             |
| -a     | <password>    | Password for Redis Auth                                      |                                              |
| -c     | <clients>     | Number of parallel connections (default 50)                  | 指定并发连接数                               |
| -n     | <requests>    | Total number of requests (default 100000)                    | 指定请求数                                   |
| -d     | <size>        | Data size of SET/GET value in bytes (default 2)              | 以字节的形式指定 SET/GET 值得数据大小        |
| -dbnum | <db>          | SELECT the specified db number (default 0)                   | 指定库，默认0号库                            |
| -k     | <boolean>     | 1=keep alive 0=reconnect (default 1)                         | 1=keep alive                                 |
| -r     | <keyspacelen> | Use random keys for SET/GET/INCR, random values for SADD<br/>  Using this option the benchmark will expand the string __rand_int__<br/>  inside an argument with a 12 digits number in the specified range<br/>  from 0 to keyspacelen-1. The substitution changes every time a command<br/>  is executed. Default tests use this to hit random keys in the<br/>  specified range. | SET / GET / INCR 使用岁就key，SADD使用随机值 |
| -P     | <numreq>      | Pipeline <numreq> requests. Default 1 (no pipeline).         | 通过管道传输 <numreq> 请求                   |
| -q     |               | Quiet. Just show query/sec values                            | 强制退出redis，仅显示 query/sec的值          |
| --csv  |               | Output in CSV format                                         | 以csv格式输出                                |
| -l     |               | Loop. Run the tests forever                                  | 循环，永久执行测试                           |
| -t     | <tests>       | Only run the comma separated list of tests. The test<br/>names are the same as the ones produced as output. | 仅运行以逗号分隔的测试命令列表               |
| -I     |               | Idle mode. Just open N idle connections and wait.            | idle模式。仅打开N个idle连接并等待            |



简单测试命令

```
redis-benchmark -q -n 100000
```





# 基础知识

1.  Redis默认有16个库，配置文件的 databases 控制，默认使用0好库，可以使用select 进行切库，DBSIZE 命令查看库大小
2.  `keys *` 命令查看库所有的key
3.  `flushall` 清空所有库数据，`flushdb` 清空当前库数据
4.  Redis是单线程的，因为Redis是基于内存操作的，CPU不是Redis的性能瓶颈，Redis的瓶颈是机器的内存和网络带宽，既然可以使用单线程来实现所以就使用单线程







