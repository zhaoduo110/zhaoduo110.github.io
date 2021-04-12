---
title: MySQL知识梳理-10.锁
abbrlink: 9333edc7
date: 2021-04-07 10:54:16
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
---





# 锁的作用

解决资源竞争的问题，使得资源可以独享





# 表锁与行锁

## 对比

锁定粒度：表锁 > 行锁

加锁效率：表锁 > 行锁

冲突概率：表锁 > 行锁

并发性能：表锁 < 行锁



**MyISAM支持的锁**

表锁



**InnoDB支持的锁**

表锁和行锁



**行锁**

- 共享锁（Shared Locks）

  - 又称`读锁`，顾名思义共享锁就是多个事务对于统一数据可以共享一把锁，都能访问到数据，但是只能读不能修改和删除
  - 加锁方式：`SELECT * FROM student WHERE id = 1 LOCK IN SHARE MODE;`
  - 释放锁：commit/rollback。即事务提交或者回滚

- 排他锁（Exclusive Locks）

  - 又称为`写锁`，排他锁不能与其他锁并存，如一个事务获取了一个数据行的排他锁，其他事务就不能再获取该锁的行（共享锁、排他锁），只有该获取了排他锁的事务是可以对数据行进行读取和修改的

  - 加锁方式
    - 自动：DELETE / UPDATE / INSERT 默认加上X锁
    - 手动：SELECT * FROM student WHERE id = 1 FOR UPDATE;
  - 释放锁：commit/rollback。即事务提交或者回滚

表锁

意向锁是由数据引擎自己维护的，用户无法手动操作意向锁

- 意向共享锁（Intention Shared Lock）
  - 简称 IS 锁，表示事务准备给数据行加入共享锁，也就是说一个数据行加共享锁前必须先取得该表的 IS 锁
- 意向排他锁（Intention Exclusive Lock）
  - 简称 IX 锁，表示事务准备给数据行加入排他锁，说明事务在一个数据行加排他锁前必须先取得该表的 IX 锁















