---
title: 阿里云性能测试PTS简单使用
abbrlink: b9dafd7e
date: 2020-07-01 15:40:46
top_img: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/5a4dbec0c6bbc.jpg
cover: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/5a4dbec0c6bbc.jpg
tags: 测试
---



# 简介

阿里云性能测试 PTS（Performance Testing Service）是具备强大的分布式压测能力的 SaaS 压测平台，可模拟海量用户的真实业务场景，全方位验证业务站点的性能、容量和稳定性

产品文档：https://help.aliyun.com/document_detail/145501.html



# 创建压测

## 场景

一个场景里面可以包含多个串联链路，每个串联链路中可以包含多个请求

串联链路中的请求可以通过手动一个个添加，也可以直接添加串联链路模板



### 简单demo

还是以总控登录接口举例

#### 创建压测场景

PTS控制台 --> 创建压测 --> 创建PTS场景

1. 输入场景名

##### 设置请求

可以自定义也可以直接添加API模板

**基本信息**

1. 输入压测的URL，请求方式，超时时间，是否允许302跳转

![阿里云性能测试 PTS-创建压测基本信息](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测基本信息.png)

**定义请求头和请求内容**

![阿里云性能测试 PTS-创建压测请求内容](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测请求内容.png)

**定义出参**

返回参数，可以定义也可以不定义

**检查点(断言)**

即可以根据响应结果做一些判断是否符合预期等



##### 施压配置

基本设置，最大并发，压测总时长，选择要压测的链路，设置链路最大并发权重等

![阿里云性能测试 PTS-创建压测施压配置](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测施压配置.png)



##### 保存配置&保存去压测

配置完成后点击保存配置

如果想直接进行压测则点击右上角保存去压测就可以开始一个简单的压测



**多链路、多个请求不做详细讲解**



## JMeter压测

保存JMeter上配置好的测试计划(jmx文件)

### 创建JMeter压测

创建压测 --> JMeter压测 

#### 场景配置

1. 输入场景名
2. 上传jmx文件，如果有其他的jar包或者数据文件也需要上传
3. 选择Jmeter版本

![阿里云性能测试 PTS-创建压测-JMeter压测场景配置](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测-JMeter压测场景配置.png)



#### 施压配置

![阿里云性能测试 PTS-创建压测-JMeter压测施压配置](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测-JMeter压测施压配置.png)





#### 保存配置&保存去压测





## 云端录制

和JMeter的HTTP代理服务器一样的

#### 录制设置

创建压测 --> 云端录制 

![阿里云性能测试 PTS-创建压测-云端录制设置](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测-云端录制设置.png)



#### 录制

点击开始录制后，手机网络设置代理

然后就可以开始进行录制了

![阿里云性能测试 PTS-创建压测-云端录制](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测-云端录制.png)



#### 导入到场景

![阿里云性能测试 PTS-创建压测-云端录制导入到场景](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/阿里云性能测试 PTS-创建压测-云端录制导入到场景.png)







# 压测

PTS控制台 --> 压测场景 --> 选择要压测的场景点击启动





# 压测报告