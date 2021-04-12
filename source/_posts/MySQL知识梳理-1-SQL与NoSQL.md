---
title: MySQL知识梳理-1.SQL与NoSQL
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218907854.jpg'
abbrlink: c84d94b
date: 2021-03-11 10:13:12
---





# SQL

关系型数据库



## 常见SQL

- MySQL
- Oracle
- SQLServer
- PostGreSQL



## 特点

- 数据结构化存储在二维表中
- 支持事务的原子性（A）、一致性（C）、隔离性（I）、持久性（D）
- 支持使用SQL语言对存储在其中的数据进行操作

## 适用场景

- 数据之间存在着一定关系，需要关联查询数据的场景
- 需要事务支持的业务场景
- 需要使用SQL语言灵活操作数据的场景

## 选型

- 数据库使用的广泛性
- 数据库的可扩展性
- 数据库的安全性和稳定性
- 数据库所支持的操作系统
- 数据库的使用成本
  - 本身的成本
  - 学习、使用的成本



### MySQL

#### 可扩展性

- 支持基于二进制日志的逻辑复制
- 存在多种第三方数据库中间层，支持读写分离及分库分表

#### 安全性和稳定性

- MySQL主从复制集群可达到99%的可用性
- 配合主从复制高可用架构可以达到99.99%的可用性
- 支持对存储在MySQL的数据进行分级安全控制

#### 支持的操作系统

- 支持Linux系统
- 支持Windows系统

#### 使用成本

- 社区版免费
- 使用人员众多，社区活跃，可以方便的获取技术支持





# NoSQL

Not only SQL



## 常见NoSQL

- HBase
- MongoDB
- Redis
- Hadoop

## 特点

- 存储结构灵活，没有固定的结构
- 对事务的支持比较弱，但对数据的并发处理性能高
- 大多不使用SQL语言操作数据



## 适用场景

- 数据结构不固定的场景
- 对事务要求不高，但读写并发比较大的场景
- 对数据的处理操作比较简单的场景