---
title: Dockerfile
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-v92g33.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/wallhaven-v92g33.jpg
abbrlink: 5624fad4
date: 2021-11-26 14:43:48
---





# Dockerfile介绍

dockerfile是docker用于自动构建镜像的一个文本文档。这个文档由一行行命令语句组成，并且`支持以 # 开头的注释行`。

一般的，Dockerfile 分为四部分：基础镜像信息、维护者信息、镜像操作指令和容器启动时执行指令



**规范**

1. Dockerfile中所用的所有文件一定要和Dockerfile文件在同一级父目录下，可以为Dockerfile父目录的子目录
2. Dockerfile中相对路径默认都是Dockerfile所在的目录
3. Dockerfile中一定要惜字如金，能写到一行的指令，一定要写到一行，单行过长时使用 \ 进行换行。原因是分层构建，联合挂载这个特性。Dockerfile中每一条指令被视为一层，为了避免分层太多，尽可能少的使用行
4. Dockerfile中指令大写（约定俗成）。虽然小写一样能识别，但最好使用大写



# 指令



> ## FROM

指定基础镜像，并且`必须是第一条指令`。

如果不以任何镜像为基础，那么写法为：FROM scratch。

接下来所写的指令将作为镜像的第一层开始。

**语法**

```dockerfile
FROM <image> 
FROM <image>:<tag> 
FROM <image>:<digest> 
```

三种写法，其中 <tag> 和 <digest>  是可选项，如果没有选择，那么默认值为 latest

**示例**

```dockerfile
FROM ubuntu
FROM ubuntu:20.04
FROM ubuntu:5d68d27cc15a80653c93d3a0b262a28112d47a46326ff5fc2dfbf7fa3b9a0ce8
```



> ## MAINTAINER

指定维护者信息

**语法**

```dockerfile
MAINTAINER <name>
```

新版docker中使用LABEL指明

**示例**

```dockerfile
MAINTAINER docker_user <docker_use@email.com>
```



> ## LABEL

为镜像指定标签

**语法**

```dockerfile
LABEL <key> <value>
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

- 一个Dockerfile种可以有多个LABEL

- 两种语法的区别在于：第一种一次只能声明一个LABEL；第二种一次可以声明多个（建议使用）。建议最好写成一行

- `LABEL会继承基础镜像种的LABEL，如遇到key相同，则值覆盖`

**示例**

```dockerfile
LABEL "com.example.vendor" "ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates
```

建议写成

```dockerfile
LABEL key1="value1" key2=value2 key3=value3 \
key4="value4" \ 
key5=value5 \
```



> ## ADD

把文件复制到镜像中

**语法**

```dockerfile
ADD <src>... <dest>
ADD ["<src>",... "<dest>"]
```

src可以是一个本地文件（目录）或者是一个本地压缩文件，还可以是一个url

- 如果为目录，会自动把目录下的文件复制过去，目录本身不会复制
- 如果src为多个文件，dest一定要是一个目录
- 如果是压缩文件，复制到容器内会自动解压缩
- 如果是url，那么ADD命令就相当于 wget 会下载该文件到 dest

dest可以是容器内的绝对路径，也可以是相对于当前工作目录（WORKDIR）的相对路径，推荐写绝对路径

**示例**

```dockerfile
ADD test1 /test
ADD test2 /
ADD test3 /test3
ADD http://nginx.org/download/nginx-1.21.4.tar.gz /usr/local/
ADD nginx-1.20.2.tar.gz /usr/local/
ADD test1 test2 test3 /temp/
ADD ["test1", "test2", "test3", "nginx-1.20.2.tar.gz", "http://nginx.org/download/nginx-1.21.4.tar.gz", "/tmp/"]
```



> ## COPY

把文件复制到镜像中

与ADD的区别是COPY的只能是本地文件，其他用法一致

**语法**

```dockerfile
COPY <src>... <dest>
COPY ["<src>",... "<dest>"]
```





> ## EXPOSE

暴漏容器运行时的监听端口给外部

但是EXPOSE并不会使容器访问主机的端口，就是说无法通过访问主机的这个端口达到访问容器端口的效果。如果想使容器与主机的端口有映射关系，必须在容器启动的时候加上 -P / -p 参数

**语法**

```dockerfile
EXPOSE <port>/<tcp/udp>
```

**示例**

```dockerfile
EXPOSE 80
EXPOSE 8080/tcp
EXPOSE 8080/udp
```





> ## ENV

设置环境变量

**语法**

```dockerfile
ENV <key> <value>
ENV <key>=<value> ...
```

两种语法的区别在于：第一种一次只能声明一个LABEL；第二种一次可以声明多个（建议使用）。建议最好写成一行

**在Dockerfile内使用变量的方式**

`$varname`

`${varname}`

`${varname:-default value}` ：当变量不存在使用 - 号后面的值

`$(varname:+default value}`：不管变量存不存在都使用 + 号后面的值





> ## RUN

运行指定的命令

**语法**

```dockerfile
RUN <command>
RUN ["executable", "param1", "param2"]
```

第一种后边直接跟shell命令

在linux操作系统上默认 `/bin/sh -c`

在windows操作系统上默认 `cmd /S /C`

第二种是类似于函数调用。可将executable理解成为可执行文件，后面就是两个参数。



> ## CMD

容器启动时要运行的命令

**语法**

```dockerfile
CMD ["executable","param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
```

第一种和第二种都是可执行文件加上参数的形式

第三种是shell这种执行方式和写法

**RUN VS CMD**

RUN是构件容器时运行的命令以及提交运行结果 

CMD是容器启动时执行的命令，在构件时并不运行，构件时紧紧指定了这个命令到底是个什么样子





> ## ENTRYPOINT

启动时的默认命令

**语法**

```dockerfile
ENTRYPOINT ["executable", "param1", "param2"]  
ENTRYPOINT command param1 param2
```

第一种就是可执行文件加参数

第二种就是写shell



**CMD VS ENTRYPOINT**

这俩命令可以配合使用
相同点：

- 只能写一条，如果写了多条，那么只有最后一条生效
- 容器启动时才运行，运行时机相同

不同点：

- ENTRYPOINT不会被运行的command覆盖，而CMD则会被覆盖

  如果在Dockerfile种同时写了ENTRYPOINT和CMD，并且CMD指令不是一个完整的可执行命令，那么CMD指定的内容将会作为ENTRYPOINT的参数

  ```dockerfile
  FROM ubuntu
  ENTRYPOINT ["top", "-b"]
  CMD ["-c"]
  
  # 当根据生成的镜像启动容器时
  # 会执行 top -b -c
  ```

  如果在Dockerfile种同时写了ENTRYPOINT和CMD，并且CMD是一个完整的指令，那么它们两个会互相覆盖，谁在最后谁生效

  ```dockerfile
  FROM ubuntu
  ENTRYPOINT ["top", "-b"]
  CMD ls -al
  
  # 当根据生成的镜像启动容器时
  # 会执行 ls -al
  # top -b 不会执行
  ```

  CMD和ENTRYPOINT使用的官方示意图

  |                            | NO ENTRYPOINT              | ENTRYPOINT exec_entry p1_entry | ENTRYPOINT ["exec_entry", "p1_entry"]          |
  | -------------------------- | -------------------------- | ------------------------------ | ---------------------------------------------- |
  | NO CMD                     | 不支持                     | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry                            |
  | CMD ["exec_cmd", "p1_cmd"] | exec_cmd p1_cmd            | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry exec_cmd p1_cmd            |
  | CMD ["p1_cmd", "p2_cmd"]   | p1_cmd p2_cmd              | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry p1_cmd p2_cmd              |
  | CMD exec_cmd p1_cmd        | /bin/sh -c exec_cmd p1_cmd | /bin/sh -c exec_entry p1_entry | exec_entry p1_entry /bin/sh -c exec_cmd p1_cmd |

  



> ## VOLUME

可实现挂载功能，可以将宿主机目录挂载到容器中

**语法**

```dockerfile
VOLUME ["/data"]
```

```dockerfile
VOLUME ["/var/log/"] 
VOLUME /var/log 
VOLUME /var/log /var/db
```



> ## USER

设置启动容器的用户，可以是用户名或UID

**语法**

```dockerfile
USER daemo
USER UID
```

注意：如果设置了容器以daemon用户去运行，那么RUN, CMD 和 ENTRYPOINT 都会以这个用户去运行,
使用这个命令一定要确认容器中拥有这个用户，并且拥有足够权限



> ## WORKDIR

设置工作目录，可以多次使用，对当前WORKDIR接下来的RUN、CMD、ENTRYPOINT、COPY、ADD生效。

多次使用时，如果当时使用的是相对路径，则是相对于当前的工作目录（官方说是相对于前一个WORKDIR指令的路径，一样的意思）

如果目录不存在则会创建（即使后续没有使用）

**语法**

```dockerfile
WORKDIR /path/to/workdir
```

**示例**

1. 正常使用

   ```dockerfile
   WORKDIR /a
   WORKDIR b
   WORKDIR c
   RUN pwd
   ```

​		pwd执行的结果是/a/b/c

2. WORKDIR也可以解析环境变量

   ```dockerfile
   ENV DIRPATH /path
   WORKDIR $DIRPATH/$DIRNAME
   RUN pwd
   ```

​		pwd的执行结果是/path/$DIRNAME



> ## ARG

设置变量，ARG命令定义了一个变量，在docker build创建镜像的时候，使用 --build-arg =来指定参数。支持定义多个

如果用户在build镜像时指定了一个参数但是并没有定义在Dockerfile中，将有一个Warning提示如下：

```Warning
[Warning] One or more build-args [foo] were not consumed.
```

`ARG变量定义从Dockerfile中定义它的行开始生效，而不是从参数在命令行或其他地方的使用开始生效`

`使用ENV指令定义的环境变量总是覆盖同名的ARG指令`

**语法**

```dockerfile
ARG <name>[=<default value>]
```

**示例**

```dockerfile
FROM busybox
ARG user1
ARG buildno=1
```



**Docker的预定义变量**

- `HTTP_PROXY`
- `http_proxy`
- `HTTPS_PROXY`
- `https_proxy`
- `FTP_PROXY`
- `ftp_proxy`
- `NO_PROXY`
- `no_proxy`

可以直接使用

```
docker build --build-arg HTTPS_PROXY=https://my-proxy.example.com .
```



> ## ONBUILD

这个命令只对当前镜像的子镜像生效。在构建当前镜像的子镜像时执行的指令，所有Dockerfile支持的指令都可以

比如当前镜像构建完成后 是 image1:1.0.0，那么所有Dockerfile里面 把 image1:1.0.0作为基础镜像的，在构建镜像时都会执行image1:1.0.0镜像的Dockerfile中声明的 ONBUILD，并且是在 FROM后马上执行

**语法**

```dockerfile
ONBUILD [INSTRUCTION]
```

**示例**

```dockerfile
ONBUILD RUN ls -al
```





> ## STOPSIGNAL

STOPSIGNAL命令是的作用是当容器停止时给系统发送什么样的指令，默认是15

**语法**

```dockerfile
STOPSIGNAL signal
```





> ## HEALTHCHECK

容器健康状况检查命令

**语法**

```dockerfile
 HEALTHCHECK [OPTIONS] CMD command
 HEALTHCHECK NONE
```

第一种是在容器内部运行一个命令来检查容器的健康状况

第二种是在基础镜像中取消健康检查命令

[OPTIONS]的选项支持以下三中选项：

- –interval=DURATION 两次检查默认的时间间隔为30秒
- –timeout=DURATION 健康检查命令运行超时时长，默认30秒
- –retries=N 当连续失败指定次数后，则容器被认为是不健康的，状态为unhealthy，默认次数是3

注意：

HEALTHCHECK命令只能出现一次，如果出现了多次，只有最后一个生效。

CMD后边的命令的返回值决定了本次健康检查是否成功，具体的返回值如下：

- 0: success - 表示容器是健康的
- 1: unhealthy - 表示容器已经不能工作了
- 2: reserved - 保留值

**示例**

```dockerfile
HEALTHCHECK --interval=5m --timeout=3s 
CMD curl -f http://localhost/ || exit 1
```

健康检查命令是：curl -f http://localhost/ || exit 1

两次检查的间隔时间是5秒

命令超时时间为3秒

