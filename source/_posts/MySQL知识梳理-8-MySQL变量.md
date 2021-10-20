---
title: MySQL知识梳理-8.MySQL变量
abbrlink: e674ae5a
date: 2021-04-02 14:22:27
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218831023.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218831023.jpg'
---



注意

1. 变量的赋值 都可以使用 = ，但是为了区别于 = 比较运算符，强烈建议使用 := 来进行赋值



# 系统变量

系统内部定义的变量，对于所有的用户都有效

## 查看系统变量

```
-- 查看所有或正则表达式匹配指定变量
show variables [like 'pattern']
-- 查询某个系统变量
select @@变量名
```

## 修改系统变量

### 局部修改

临时生效，只对当前会话有效

`部分系统变量只能进行全局修改即 SET GLOBAL 方式`

**语法**：

```
set 变量名 := 新值;
```

### 全局修改

对所有的客户端，已经存在的会话不会生效，只有新开的才有效

注意：

这种方法当MySQL服务端重启之后还是会失效，要想永久生效需要修改配置文件（重启MySQL服务生效）

基本语法：

```
set global 变量名 := 值；
或者
set @@global.变量名 := 值
```



# 会话变量（用户变量）

会话变量跟mysql客户端是绑定的，设置的变量，只对当前用户使用的客户端生效

**语法**

定义变量

```
set @变量名 := 值;
```

mysql 允许将数据从表中取出存储到变量中；`查询得到的数据必须只能是一行数据（一个变量对应一个字段值），因为mysql没有数组`

赋值且查看赋值过程：

```
SELECT @变量1 := 字段1,@变量2 := 字段2 FROM 数据表 WHERE 条件
```

只赋值不看过程（以下两种方法都行）

```
SELECT 字段1,字段2... FROM 数据源 WHERE 条件 INTO @变量1,@变量2...
SELECT 字段1,字段2... INTO @变量1,@变量2... FROM 数据源 WHERE 条件 
```

查看变量

```
select @变量名字 [AS alias]
```



# 局部变量

局部变量是表达式语句中最基本的元素，可以用来临时存储数据。在存储过程和函数中都可以定义和使用局部变量

局部变量的作用范围是 BEGIN...END 程序段中

**语法**

定义变量

```
DECLARE var_name[,...] type [DEFAULT value]
```

- DECLARE 关键字是用来声明变量的；
- var_name 参数是变量的名称，这里可以同时定义多个变量；
- type 参数用来指定变量的类型；
- DEFAULT value 子句将变量默认值设置为 value，没有使用 DEFAULT 子句时，默认值为 NULL。

变量赋值

```
SET var_name := expr[,var_name = expr]...
```

- SET 关键字用来为变量赋值；
- var_name 参数是变量的名称；
- expr 参数是赋值表达式。

局部变量也可以通过 SELECT...INTO 语句为变量赋值，方式同

赋值且查看赋值过程：

```
SELECT @变量1 := 字段1,@变量2 := 字段2 FROM 数据表 WHERE 条件
```

只赋值不看过程（以下两种方法都行）

```
SELECT 字段1,字段2... FROM 数据源 WHERE 条件 INTO 变量1，变量2...
SELECT 字段1,字段2... INTO 变量1，变量2... FROM 数据源 WHERE 条件 
```











