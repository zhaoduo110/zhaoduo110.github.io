---
title: MySQL知识梳理-7.SQL优化-索引
abbrlink: eea2a833
date: 2021-03-29 09:46:15
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
---



# 什么是索引



Innodb的索引类型

- 普通索引
- BTree索引
  - 以B+树的结构存储索引数据
  - 适用于全值匹配(完全匹配)的查询。 class_name='mysql' 或者 class_name IN ('mysql', 'bbb') 
  - 适合处理范围查找 ：between and 、> 、<
  - 在使用联合索引时，如果只用到了联合索引中的某些而不是所有列进行匹配，则只能从索引的最左侧列开始匹配查找列，如果联合索引中的列都用到了则会忽略顺序，可以命中索引
- Hash索引
- 全文索引
- 空间索引



BTree索引的限制

- 只能从最左侧开始按索引键的顺序使用索引，不能跳过索引列
- !=、 <> (不等于) 和 NOT IN 操作无法使用索引
- 索引列上不能使用表达式或函数



复合索引的建立原则

- 区分度最高的列放在联合索引的最左侧
- 使用最频繁的列放到联合索引的最左侧
- 尽量把字段长度小的列放在联合索引的最左侧



使用索引的误区

- 索引越多越好
- 使用IN查询不能用到索引
- 查询过滤顺序必须同索引列顺序相同才可以用到索引