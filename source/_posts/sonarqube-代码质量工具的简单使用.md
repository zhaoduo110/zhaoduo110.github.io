---
title: PHP设计模式介绍
categories:
  - - 测试
tags:
  - 测试
  - 代码审计
abbrlink: 68d7117c
date: 2020-05-22 16:54:33
top_img: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218957853.jpg
cover: https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218957853.jpg
---
# 安装







# 使用

## 项目配置

#### 新建项目

![代码质量工具sonarqube新建项目](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/代码质量工具sonarqube新建项目.png)

#### 设置项目名称

![代码质量工具sonarqube新建项目设置](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/代码质量工具sonarqube新建项目设置.png)

#### 设置令牌

可以使用已有Token也可以新建

Token可以使用 md5('项目名')

创建完成后点击继续



## 扫描分析

### Demo

![代码质量工具sonarqube项目分析](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/代码质量工具sonarqube项目分析.png)

1. 选择语言

2. 选择操作系统

3. 下载对应平台的扫描器

   下载页面：[https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/)

   linux上需要执行

   1. 下载

      ```
      cd /usr/local
      wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.3.0.2102-linux.zip
      ```

   2. 解压包

      ```
      unzip sonar-scanner-cli-4.3.0.2102-linux.zip
      ```

   3. 添加环境变量

      ```
      vim /etc/profile
      # 末尾添加以下
      export PATH=/usr/local/sonar-scanner-4.3.0.2102-linux/bin:$PATH
      # 保存退出
      # 配置生效
      source /etc/profile
      ```

4. 扫描项目

   在项目目录下执行界面上给的扫描命令

5. 扫描完成后即可在管理界面对应的项目中看到结果



### sonar-scanner扫描命令的配置

| 配置项     | 作用                                                     | 取值   |
| ---------- | -------------------------------------------------------- | ------ |
| projectKey | 项目名称                                                 |        |
| sources    | 要扫描的目录                                             |        |
| host.url   | sonar服务器ip或者域名                                    |        |
| login      | 管理后台设置的令牌                                       |        |
| exclusions | 要排除的目录或者文件，不需要扫描的。<br>测试无效，未解决 | vendor |













