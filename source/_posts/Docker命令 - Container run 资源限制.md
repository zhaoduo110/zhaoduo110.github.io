---
title: Docker命令 - Container run 资源限制
abbrlink: 4e368c5e
date: 2021-11-17 14:57:45
categories:
  - Docker
tags:
  - Docker
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/bd0268b8e42e8d626097359610de5e49-1400.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/bd0268b8e42e8d626097359610de5e49-1400.jpg
---







# 容器运行的资源使用限制

可以调整容器的性能参数

**参数列表**

## 内存相关

内存限制

> -m
> --memory=""

格式 `<number>[<unit>]` ：number是正整数，unit可以是 b, k, m, or g，最小值是4M

**示例**

首先看一个没有主动设置内存限制的容器

启动一个centos容器

```bash
docker container run -it --rm centos /bin/bash
```

新建窗口查看容器状态

```bash
docker stats containerId
```

![image-20211117112010241](https://could-res-1252778021.file.myqcloud.com/img/20211117112010.png)

可以看到当前容器的总内存为1.9G，这个值和docker-desktop上看到的（setting->resources->advanced）一样

为什么是这个值？

参考：[Windows image build Docker runs out of memory on Windows 10 · Issue #31604 · moby/moby (github.com)](https://github.com/moby/moby/issues/31604)

这里说的是windows10使用的默认隔离级别为hyper-v，hyper-v具有默认限制，[hyper-v说明](https://docs.microsoft.com/zh-cn/virtualization/hyper-v-on-windows/about/)

![image-20211117110103346](https://could-res-1252778021.file.myqcloud.com/img/20211117110103.png)



`由于在windows10上面使用内存无效，所以这个实验放到centos上做`



再启动一个容器

```bash
docker container run -it --rm -m 100m centos /bin/bash
# 或者
docker container run -it --rm --memory 100m centos /bin/bash
```

新建窗口查看容器状态

```bash
docker stats ed9e9186e9a3
```

![image-20211117113149232](https://could-res-1252778021.file.myqcloud.com/img/20211117113149.png)



> --memory-swap=""

内存的总使用量，memory+swap，使用方法和 --memory一样

只有在设置了 `--memory` 时才有意义。使用 swap 允许容器在耗尽所有可用的 RAM 时，将多余的内存需求写入磁盘。对于经常将内存交换到磁盘的应用程序，性能会受到影响。

它的设置会产生复杂的影响

- 如果 `--memory-swap` 设置为正整数，则必须设置 `--memory` 和 `--memory-swap`。 `--memory-swap` 表示可以使用的 memory 和 swap 总量,  `--memory` 控制 no-swap 的用量。 所以，如果设置 `--memory="300m"` 和 `--memory-swap="1g"`,  容器可以使用 300m memory 和 700m (`1g - 300m`) swap。

- 如果 `--memory-swap` 设置为 `0`, 该设置被忽略，该值被视为未设置。

- 如果 `--memory-swap` 的值等于 `--memory` 的值, 并且 `--memory` 设置为正整数， **则容器无权访问 swap**。这是因为 `--memory-swap` 是可以使用组合的 Memory 和 Swap，而 `--memory` 只是可以使用的 Memory。

- 如果 `--memory-swap` 不设置, 并且 `--memory` 设置了值, 容器默认可以使用 `--memory` 两倍的 Swap（如果主机容器配置了 Swap）。

  示例： 设置 `--memory="300m"` 并且不设置 `--memory-swap`，容器可以使用 300m memory 和 600m swap。

- 如果 `--memory-swap` 设置为 `-1`，允许容器无限制使用 Swap。

`在容器内部，像 free 等工具报告的是主机的可用 Swap，而不是容器内可用的。不要依赖于 free 或类似工具的输出来确定是否存在 Swap。`

**示例**

```bash
docker container run -it --rm --memory="100m" --memory-swap="500m" centos /bin/bash
```



>--memory-reservation

内存软限制（对应--memory硬限制），使用方式和 --memory 一样。

在正常情况下，容器可以根据需要使用尽可能多的内存，并且只受 -m 设置的硬限制的约束。当设置内存软限制时，Docker检测内存争用或内存不足时会在下一次系统内存回收的时候尝试将容器内存压缩到限制范围内

`内存软限制必须小于硬限制，否则硬限制优先。预留为0与不设置预留相同。默认情况下(无保留)`

**示例**

```bash
docker container run -it --rm --memory="100m" --memory-reservation 50m  centos /bin/bash
```



> --kernel-memory=""

内核内存限制，通过限制内核内存，可以防止在内核内存使用率过高时创建新的进程。使用方式和 --memory 一样，最小4M

内核内存限制是就分配给容器的总内存而言的。

内核内存与用户内存本质上是不同的，内核内存不能交换出去。由于无法进行交换，容器可能会消耗过多的内核内存，从而阻塞系统服务。

内核内存包括

- 栈页
- slab 页
- 套接字内存压力
- tcp内存压力



一般可选方案

- 无限内存，无限内核内存

  默认行为。

- 无限内存，有限内核内存

  当所有 cgroup 所需的内存量大于主机上实际存在的内存量时，它是合适的。可以将内核内存配置为永远不会超过主机上可用的内存，而需求更多内存的容器需要等待它。

- 有限内存，无限内核内存

  整体内存有限，但内核内存不是。

- 有限内存，有限内核内存

  限制用户和内核内存对于调试与内存相关的问题非常有用，如果容器使用意外数量的任意类型的内存，则内存不足不会影响其他容器或主机。在此设置中，如果内核内存限制低于用户内存限制，则内核内存不足会导致容器遇到 OOM 错误。如果内核内存限制高于用户内存限制，则内核限制不会导致容器遇到 OOM。

当打开任何内核内存限制时，主机会根据每个进程跟踪 “高水位线” 统计信息，因此您可以跟踪哪些进程（在本例中为容器）正在使用多余的内存。通过查看主机上的 `/proc/<PID>/status`，可以在每个进程中看到这一点。



> --memory-swappiness=""

调优容器的内存交换行为。接受0到100之间的整数。

默认情况下，主机内核可以交换容器使用的匿名页面的百分比。

- 值为 0 时，关闭匿名页交换
- 值为 100 时，将所有匿名页设置为可交换
- 默认情况下，如果不设置 `--memory-swappiness`, 该值从主机继承





## CPU相关

> -c, --cpu-shares=0





> --cpus=0.000



> --cpu-period=0





> --cpuset-cpus=""



> --cpuset-mems=""



> --cpu-quota=0





> --cpu-rt-period=0





> --cpu-rt-runtime=0



