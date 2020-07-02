---
title: 性能测试工具JMeter简单使用
abbrlink: 1053ab1d
date: 2020-07-01 15:41:43
top_img: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/fdafcf5fe7ef277b7af4e59127a0fa78.jpg
cover: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/fdafcf5fe7ef277b7af4e59127a0fa78.jpg
tags: 测试
---



## 下载安装

### 环境需求

- Java环境（不同JMeter版本对Java版本的要求不同）

---



### 下载

- 下载页面：[https://jmeter.apache.org/download_jmeter.cgi](https://jmeter.apache.org/download_jmeter.cgi)
- 下载地址：[https://mirror.bit.edu.cn/apache//jmeter/binaries/apache-jmeter-5.3.zip](https://mirror.bit.edu.cn/apache//jmeter/binaries/apache-jmeter-5.3.zip)

---



### 安装

直接解压zip文件即可

---



### 运行

##### 1. 找到解压目录中的 apache-jmeter-5.3\bin 目录

##### 2. windows系统直接双击 jmeter.bat 文件运行

##### 3. 运行后会先出现一个dos窗口(JMeter运行控制台)，然后会出现JMeter的界面

##### 注意：

JMeter运行后如果关闭dos窗口，JMeter就会被关闭

##### 运行成功后如下图

![JMeter运行图](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20200701155400.png)

---





## JMeter简单使用

官方文档：[https://jmeter.apache.org/usermanual/get-started.html](https://jmeter.apache.org/usermanual/get-started.html)



### 切换界面语言

工具栏 --> Options --> Choose Language --> 选择你要切换的语言

![JMeter切换界面语言](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter设置语言.gif)



### 常用组件介绍

#### 测试计划(Test Plan)

测试计划(Test Plan)是使用 JMeter 进行测试的起点，它是其它 JMeter 测试元件的容器

![JMeter测试计划组件介绍](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20200701162531.png)

- 名称（Name）：可以自定义一个有意义的名字

- 注释（Comments）：对测试计划的注释

- 用户定义的变量（User Defined Variables）：用户可以自己定义变量，在用到此变量的时候直接用 `${变量名}` 引用即可。

  例：变量名＝url，值＝http://www.baidu.com，在需要http://www.baidu.com时直接用${url}即可

- 添加目录或jar包到ClassPath（Add directory or jar to classpath）：向类路径即 `%JMETER-HOME%\lib` 中添加目录及jar包



#### 线程组(Thread Group)

代表一定数量的并发用户，它可以用来模拟并发用户发送请求

![JMeter线程组组件介绍](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter线程组组件介绍.png)

添加方法：右键单击 测试计划 --> 添加 --> 线程(用户) --> 线程组

![JMeter添加线程组](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter添加线程组.png)

- 名称（Name）：为线程组起名字
- 线程属性－线程数（Thread Properties - Number of Threads(users)）：设置发送请求的用户数目 ，即并发数

- 线程属性－ （Thread Properties - Ramp-Up Period(in second)）：线程间的时间间隔，单位是秒。即所有线程在多少时间内启动
- 线程属性－循环次数（Thread Properties - Loop Count）：请求的重复次数，如果选择后面的forever（默认），那么 请求将一直继续，如果不选择forever，而在输入框中输入数字，那么请求将重复指定的次数，如果输入1，那么请求将执行一次，如果是0，会出现问题
- 调度器配置－启动延迟：测试计划延迟多长时间启动，会覆盖启动时间
- 调度器配置－持续时间：测试计划持续多长时间，会覆盖结束时间



#### 取样器(sampler)

定义实际的请求内容，被线程组包含，我们主要用HTTP请求

![JMeter取样器组件介绍](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter取样器组件介绍.png)

- 名称：HTTP请求的名字。
- Web服务器－服务器名称或IP、端口号，脚本录制时自动添加，也可以使用“用户自定义变量”。
- HTTP请求：用默认值即可。
- 同请求一起发送参数：请求中的参数、值可以在此设置，需要用到参数化及动态数据关联，我们在后面专门介绍。
- 同请求一起发送文件：我可以制定同请求一起发送哪个文件。
- 其他任务包括：从HTML文件中获取所有内含的资源、用作监视器、Embedded URLs must match



#### 监听器(Listener)

监听器(Listener)负责收集测试结果，同时也被告知了结果显示的方式。

常用的包括：聚合报告、查看结果树、用表格查看结果，都支持将结果数据写入文件，其他的添加上去看看就行



#### 逻辑控制器(Logic Controller)

逻辑控制器(Logic Controller)可以自定义JMeter发送请求的行为逻辑，它与Sampler结合使用可以模拟复杂的请求序列

- 循环控制器可以设置请求的循环次数或永远循环（如果选中永远的话）。

- 事务控制器可以将多个请求放在同一个事务中。如果选中Gegerate parent sample，则聚合报告中只显示事务控制器的数据，而不会显示其中的各个请求的数据，反之则全部显示



#### 断言(Assertions)

断言(Assertions)可以用来判断请求响应的结果是否如用户所期望的。它可以用来隔离问题域，即在确保功能正确的前提下执行压力测试。这个限制对于有效的测试是非常有用的



#### 配置元件(Config Element)

配置元件(Config Element)维护Sampler需要的配置信息，并根据实际的需要会修改请求的内容。我们主要在参数化中用到CSV Data Set Config



#### 前置处理器(Pre Processors)和后置处理器(Post Processors)

前置处理器(Pre Processors)和后置处理器(Post Processors)负责在生成请求之前和之后完成工作。前置处理器常常用来修改请求的设置

后置处理器则常常用来处理响应的数据。我们主要在动态关联中用到后置处理器的正则表达式提取器



#### 定时器(Timer)

定时器(Timer)负责定义请求之间的延迟间隔





### 简单的demo

比如请求百度的主页

##### 第一步：测试计划

##### 第二步：创建线程组

可以在线程组设置线程数等

##### 第三步：添加取样器 -->  HTTP请求

![JMeter简单emo-请求百度](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter简单demo-请求百度.png)

##### 第四步：添加监听器 --> 查看结果树

##### 第五步： 运行线程组

![JMeter简单demo-运行线程组](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter简单demo-运行线程组.png)

##### 第六步： 运行后，每完成一个请求即可在查看结果树里面看到请求状态及结果



### 模拟线上接口的demo

##### 第一步：测试计划

##### 第二步：创建线程组

可以在线程组设置线程数等

![JMeter演示线上接口测试](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示线上接口测试.png)

##### 第三步：如果接口返回有cookie(比如用户登录需要保留用户登录状态)或者需要查看返回信息头的话，可以添加 HTTP信息头管理器和HTTP Cookie管理器

右键线程组 --> 添加 --> 配置元件 --> 对应的组件即可

##### 第四步：添加取样器 -->  HTTP请求

1. 线程组设置
2. 设置协议
3. 设置服务器ip或者域名
4. 设置请求端口
5. 设置请求方式
6. 设置请求路径
7. 设置请求参数

![JMeter演示线上接口测试-设置请求](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示线上接口测试-设置请求.png)



也可在线程组中创建多个HTTP请求即可模拟一次完整的业务调用

##### 第五步：设置HTTP请求的监听器（注意： 可以对每个请求添加查看结果树，多个请求也可以直接给线程组添加结果树）

右键HTTP请求 --> 添加 --> 监听器 --> 查看结果树

##### 第五步： 运行线程组

运行完可以查看所有的请求的运行结果



### 模拟线上某个业务场景（多个接口）的demo

使用代理录制的方式，方便快捷

##### 第一步：测试计划

##### 第二步：创建线程组

可以在线程组设置线程数等

![JMeter演示线上接口测试](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示线上接口测试.png)

##### 第三步：添加代理服务器

右键测试计划 --> 添加 --> 非测试元件 --> HTTP代理服务器

##### 第四步：设置代理服务器

![JMeter演示代理服务器录制](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示代理服务器录制.png)

启动成功后效果如下

![JMeter演示代理服务器录制-启动代理服务器](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示代理服务器录制-启动代理服务器.png)



##### 第五步：手机连接代理服务器

由于用的是公司电脑，手机连接同网络下的wifi，然后代理设置JMeter所在的电脑ip加8888端口即可

APP的设置同设置抓包工具的代理时一样

![JMeter演示代理服务器录制-手机连接代理](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示代理服务器录制-手机连接代理.png)



##### 第六步：开始录制

和正常运行app一样，比如我要抓取整个登录行为调用的接口，即可在app正常走登录流程

登录完成后所有的接口都会被代理服务器录制到对应的线程组中

![JMeter演示代理服务器录制-录制完成](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示代理服务器录制-录制完成.png)



##### 第七步： 关闭代理服务器

##### 第八步：根据实际情况设置线程组。比如添加HTTP cookie管理器，添加查看结果树等

##### 第九步：运行线程组

![JMeter演示代理服务器录制](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/JMeter演示代理服务器录制-运行线程组.png)



---





以上都为一些基本操作，JMeter还支持 比如 

用户自定义变量（可用于定义全局变量，比如域名）

用户参数

自定义函数

读取csv数据、读取数据库数据（，作为请求参数，更接近真实用户请求）

对数据库直接进行测试

等等



## JMeter高级功能

### 参数化设置



