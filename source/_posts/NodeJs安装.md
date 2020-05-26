---
title: NodeJs安装
---

## node、npm、cnpm、nvm

#### 区别

- nodejs：在项目开发时的所需要的代码库

- npm：nodejs 包管理工具

    > 在安装的 nodejs 的时候，npm 也会跟着一起安装，它是包管理工具
    >
    > npm 管理 nodejs 中的第三方插件

- cnpm：和npm一样的nodejs包管理工具。淘宝团队做的国内镜像，因为npm的服务器位于国外可能会影响安装。淘宝镜像与官方同步频率目前为 10分钟 一次以保证尽量与官方服务同步

- nvm：nodejs 版本管理工具

    > 一个 nvm 可以管理很多 node 版本和 npm 版本

#### 关系

- nvm 管理 nodejs 和 npm 的版本

- npm 可以管理 nodejs 的第三方插件





---





## 安装NVM

> nvm是一个来管理node的工具，方便我们在开发过程中node版本的切换

官网：http://www.nvm-consultants.co.uk/

GitHub下载地址：https://github.com/coreybutler/nvm-windows/releases

### 安装

下载后解压出来的exe文件双击进行傻瓜式安装即可

**注意：**

1. 安装目录不要有中文（这个一定要注意）
2. 如果已经安装了nodejs，在第二次选择文件夹的时候选择nodejs的目录并同意让nvm管理nodejs

### 配置环境变量

新版默认安装就配置了，可以去环境变量中确认一下，没有的话需要手动添加

### 测试是否安装成功

```bash
nvm -v
```

### 配置淘宝镜像

安装目录中找到settings.txt文件并修改

```
root: E:\dev\nvm
path: E:\dev\nodejs
arch:64
proxy:none
node_mirror:http://npm.taobao.org/mirrors/node/
npm_mirror:https://npm.taobao.org/mirrors/npm/
```

### 命令

| 命令                             | 作用                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| nvm list                         | 查找本电脑上所有的node版本                                   |
| nvm list installed               | 查看已经安装的node版本                                       |
| nvm list available               | 查看网络可以安装的node版本                                   |
| nvm ls                           | 列出本机所有node版本                                         |
| nvm ls-remote                    | 列出远端所有node版本                                         |
| nvm install latest               | 安装最新版本node                                             |
| nvm install <version> [arch]     | 安装指定版本 [指定位数] node                                 |
| nvm use <version>                | 切换使用指定的版本node                                       |
| nvm current                      | 显示nvm当前版本                                              |
| nvm alias <name> <version>       | 给不同的node版本号添加别名                                   |
| nvm unalias <name>               | 删除已定义的别名                                             |
| nvm reinstall-packages <version> | 在当前版本node环境下，重新全局安装指定版本号的npm包          |
| nvm on                           | 打开nodejs控制                                               |
| nvm off                          | 关闭nodejs控制                                               |
| nvm proxy                        | 查看设置与代理                                               |
| nvm node_mirror [url]            | 设置或者查看setting.txt中的node_mirror<br>如果不设置的默认是 https://nodejs.org/dist/ |
| nvm npm_mirror [url]             | 设置或者查看setting.txt中的npm_mirror<br>如果不设置的话默认的是： https://github.com/npm/npm/archive/ |
| nvm uninstall <version>          | 卸载指定的版本                                               |
| nvm use [version] [arch]         | 切换制定的node版本和位数                                     |
| nvm root [path]                  | 设置和查看root路径                                           |
| nvm version                      | 查看当前的版本                                               |





---





## NPM & Node.js

> 1. Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效
>
> 2. NPM是随同NodeJS一起安装的 `包管理工具`，能解决NodeJS代码部署上的很多问题。由于新版的nodejs已经集成了npm，所以之前npm也一并安装好了

**NodeJs、NPM中文官网：**https://nodejs.org/en/

### 常见的使用场景有以下几种

- 允许用户从NPM服务器下载别人编写的第三方包到本地使用。
- 允许用户从NPM服务器下载并安装别人编写的命令行程序到本地使用。
- 允许用户将自己编写的包或命令行程序上传到NPM服务器供别人使用

### 安装

列出所有可安装版本

```
nvm list available
```

安装最新版本

```
nvm install latest
```

执行版本指定位数进行安装

```
nvm install <version> <arch>
```





## CNPM

### 安装

使用淘宝镜像安装cnpm

```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

### 测试是否安装成功

```
cnpm -v
```









