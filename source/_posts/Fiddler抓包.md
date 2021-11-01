---
title: Fiddler抓包
categories:
  - - Debug
tags:
  - Debug
abbrlink: 'f73eaef5'
date: 2021-11-01 15:56:53
top_img: >-
  https://could-res-1252778021.file.myqcloud.com/img/Konachan.com-20sample.jpg
cover: >-
  https://could-res-1252778021.file.myqcloud.com/img/Konachan.com-20sample.jpg
---





# 下载

Fiddle官网：[Fiddler | Web Debugging Proxy and Troubleshooting Solutions (telerik.com)](https://www.telerik.com/fiddler)

插件扩展：https://www.telerik.com/fiddler/add-ons

一般下载fiddler core就够用了，fiddler everyWhere功能比较多，需要登录账号，但是需要付费（有免费时间）



# 安装

下载完之后傻瓜式安装完成即可



# 配置

`每次修改完配置建议都重启下Fiddler`

## 基础设置

### Fiddler 支持远端设备连接

记住这里设置的端口，可以修改

![image-20211101164416536](https://could-res-1252778021.file.myqcloud.com/img/20211101164416.png)



### 查看当前设备ip

获取PC的ip地址：命令行中输入:ipconfig,获取ip地址

![image-20211101164639435](https://could-res-1252778021.file.myqcloud.com/img/20211101164639.png)



### 设置Android手机

注意 

`要抓包的设备必须和Fiddler所在的机器在同一网络内并且网络互通`

安卓不同手机的操作方法步骤大致相同



- 进入手机的设置->点击进入WLAN设置->选择连接到的无线网，长按弹出选项框



![image-20211101165726012](https://could-res-1252778021.file.myqcloud.com/img/20211101165726.png)

- 选择修改网络配置：
  选择显示高级选项，将代理设置成手动，将上面获取到的ip地址和端口号填入，点击保存。这样就将我们的手机设置成功了。

![image-20211101165802090](https://could-res-1252778021.file.myqcloud.com/img/20211101165802.png)

- 下载Fiddler的安全证书
  使用Android手机的浏览器打开：[http://192.168.1.86:8888](http://10.2.145.187:8888/)（注：IP跟端口都是你自己对应的）， 点"FiddlerRoot certificate" 然后安装证书，**如图6:**

![image-20211101165843520](https://could-res-1252778021.file.myqcloud.com/img/20211101165843.png)





### 设置IOS（15）

不上图

下载好证书之后

首先到设置-->通用-->VPN与设备管理-->配置描述文件-->安装该描述文件

然后到设置-->通用-->关于本机-->证书信任设置-->找到该证书打开开关





## 支持抓取HTTPS包

选中"Decrpt HTTPS traffic",  Fiddler就可以截获HTTPS请求
选中"Allow remote computers to connect". 是允许别的机器把HTTP/HTTPS请求发送到Fiddler上来

![image-20211101164334480](https://could-res-1252778021.file.myqcloud.com/img/20211101164334.png)

`注意：`

如果这样配置之后还是不行，有可能是部分安卓系统和IOS系统的版本问题导致的证书不可能，表现为抓到的HTTPS的包在Fiddler里面看是 HTTP协议 ，请求的地址是xxxx.com:443这样的，这种情况需要使用一个fiddler扩展 `fiddlercertmaker` ，这个扩展官方有下，下载完成后关闭fiddler，运行下载的exe即可，运行完之后会在fiddler的安装目录下多一个`CertMaker.dll`的文件，然后再打开fiddler，据测试最好还是在手机上重新安装一下证书

下载地址：https://telerik-fiddler.s3.amazonaws.com/fiddler/addons/fiddlercertmaker.exe





## 支持抓取websocket包

打开Fiddler，点开菜单栏的Rules，选择Customize Rules...

这时会打开CustomRules.js文件，搜索class Handlers，在这个类中加入以下代码

```javascript
static function OnWebSocketMessage(oMsg: WebSocketMessage) {
    // Log Message to the LOG tab
    FiddlerApplication.Log.LogString(oMsg.ToString());
}
```

在log里面就可以看到websocket交互的数据

![Image](https://could-res-1252778021.file.myqcloud.com/img/20211101172607.jpg)









# 使用

### 阻塞请求 & 模拟并发

拦截请求

![image-20211101170951315](https://could-res-1252778021.file.myqcloud.com/img/20211101170951.png)



复制请求

选择请求Shift+r 会弹出对话框输入数字即可 20

![image-20211101171021331](https://could-res-1252778021.file.myqcloud.com/img/20211101171021.png)



发送请求

最后点击 GO即可

![image-20211101171041775](https://could-res-1252778021.file.myqcloud.com/img/20211101171041.png)

















