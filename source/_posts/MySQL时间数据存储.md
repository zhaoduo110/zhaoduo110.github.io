---
title: MySQL时间数据存储
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.file.myqcloud.com/img/20211102193152.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/img/20211102193152.jpg'
abbrlink: 67bf281
date: 2021-11-02 18:06:08
---





| 类型      | 格式                | 范围                                                         | 存储空间 |
| --------- | ------------------- | ------------------------------------------------------------ | -------- |
| YEAR      | YYYY                | 1901 ~ 2155                                                  | 1个字节  |
| DATE      | YYYY-MM-DD          | 1000-01-01 ~ 9999-12-31                                      | 3个字节  |
| TIME      | HH:MM:SS            | -838:59:59.000000 ~ 838:59:59.000000                         | 3个字节  |
| TIMESTAMP | YYYY-MM-DD HH:MM:SS | 1970-01-01 00:00:01.000000UTC ~ 2038-01-19 03:14:07.999999UTC | 4个字节  |
| DATETIME  | YYYY-MM-DD HH:MM:SS | 1000-01-01 00:00:00.000000 ~ 9999-12-31 23:59:59.999999      | 8个字节  |

`MySQL允许对TIME、DATETIME和TIMESTAMP类型值使用小数秒，精度最高可达微秒(6位)。要定义包含小数秒部分的列，使用语法type_name(fsp)，其中type_name是TIME、DATETIME或TIMESTAMP，而fsp是小数秒精度`



时间类型MySQL支持的所有文字表述方式：https://dev.mysql.com/doc/refman/8.0/en/date-and-time-literals.html



# YEAR

年份类型。只保存年份，占用空间小

**创建表**

- 类型支持：year 与 year(4)，注意无year(2)的定义方式
- 值的有效范围：1901 至 2155

```mysql
CREATE TABLE test_year (
	`test_year` YEAR NOT NULL DEFAULT 0 COMMENT '年份类型'
) ENGINE=INNODB CHARSET=utf8;
```



**插入值**

1. 支持整数和字符串，支持 2位数 或者 4位数

   `需要注意的是：数字0显示的是0，字符串'0'显示的是2000`

   - 2位数

     00~69 将转换为2000 ~ 2069之间

     70~99 将转换为1970 ~ 1999之间

   - 4位数

     就是正常的年份，比如1990、2021

2. 插入当前年份。year(now()) 或者 year(curdate())

   ```mysql
   INSERT INTO test_year (`test_year`) VALUE (year(now()));
   INSERT INTO test_year (`test_year`) VALUE (year(curdate()))
   ```



# DATE

日期类型。



**创建表**

默认值没找到一个好的正确的设置方法

```mysql
CREATE TABLE test_date (
	`test_date` DATE NOT NULL COMMENT '日期类型'
) ENGINE=INNODB CHARSET=utf8
```



**插入值**

- `如果插入值的年份部分为2位数，则依然遵循year类型的规则`

- 不支持只插入年月，或只插入月日等，必须是年月日都有

```mysql
INSERT INTO test_date (`test_date`) VALUE ('00-11-22');
INSERT INTO test_date (`test_date`) VALUE ('10-11-22');
INSERT INTO test_date (`test_date`) VALUE ('1950-11-22');
INSERT INTO test_date (`test_date`) VALUE (DATE(now()));
INSERT INTO test_date (`test_date`) VALUE (DATE(curdate()));

> select * from test_date;
test_date
2000-11-22
2010-11-22
1950-11-22
2021-11-03
2021-11-03
```





# TIME

时间类型

**声明方式**

> filed_name TIME[(fsp)] [NOT NULL DEFAULT .....]

fsp可以给出 0 到 6 范围内 的可选值来指定小数秒精度。值 0 表示没有小数部分。如果省略，则默认精度为 0

**创建表**

```mysql
CREATE TABLE test_time (
	`test_time` TIME NOT NULL DEFAULT 0 COMMENT '日期类型'
) ENGINE=INNODB CHARSET=utf8
```

**插入值**

```mysql
INSERT INTO test_time (`test_time`) VALUE ('25:10:10');
INSERT INTO test_time (`test_time`) VALUE (TIME(now()));
INSERT INTO test_time (`test_time`) VALUE (CURRENT_TIME);
INSERT INTO test_time (`test_time`) VALUE (TIME(CURRENT_TIME));
INSERT INTO test_time (`test_time`) VALUE (CURRENT_TIMESTAMP);
INSERT INTO test_time (`test_time`) VALUE (TIME(CURRENT_TIMESTAMP));

> SELECT * FROM test_time;
test_time
25:10:10
11:44:00
11:44:00
11:44:00
11:44:00
11:44:00
```



# DATETIME

日期时间类型

**声明方式**

> filed_name DATETIME[(fsp)] [NOT NULL DEFAULT .....]

fsp可以给出 0 到 6 范围内 的可选值来指定小数秒精度。值 0 表示没有小数部分。如果省略，则默认精度为 0

**创建表**

```mysql
CREATE TABLE test_datetime (
	`created_at` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '日期时间类型',
    `updated_at` DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '日期时间类型'
) ENGINE=INNODB CHARSET=utf8;
```

- DEFAULT CURRENT_TIMESTAMP 声明默认值为当前时间

- DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP 声明默认值为当前时间并且在行发生修改时该字段值自动更新为当前时间

`如果要把值默认为NULL，DEFAULT NULL这种方式不好使 要使用以下方式`

```mysql
CREATE TABLE ... (
	...
	`created_at` TIMESTAMP NULL COMMENT '',
    ...
) ...
```



**插入值**

```mysql
INSERT INTO test_datetime (`created_at`) VALUE ('25:10:10');
INSERT INTO test_datetime (`created_at`) VALUE ('10:10:10');
INSERT INTO test_datetime (`created_at`) VALUE ('10:10:10 10:10:10');
INSERT INTO test_datetime (`created_at`) VALUE (CURRENT_DATE);
INSERT INTO test_datetime (`created_at`) VALUE (CURRENT_TIME);
INSERT INTO test_datetime (`created_at`) VALUE (CURRENT_TIMESTAMP);
INSERT INTO test_datetime (`created_at`) VALUE (NOW());

> SELECT * FROM test_datetime;
created_at updated_at
2025-10-10 00:00:00	2021-11-03 14:18:12
2010-10-10 00:00:00	2021-11-03 14:18:12
2010-10-10 10:10:10	2021-11-03 14:18:12
2021-11-03 00:00:00	2021-11-03 14:18:12
2021-11-03 14:18:12	2021-11-03 14:18:12
2021-11-03 14:18:12	2021-11-03 14:18:12
2021-11-03 14:18:12	2021-11-03 14:18:12
```



# TIMESTAMP

日期时间类型

**声明方式**

> filed_name TIMESTAMP[(fsp)] [NOT NULL DEFAULT .....]

官方说明：服务器处理 `TIMESTAMP` 定义的方式取决于[`explicit_defaults_for_timestamp`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_explicit_defaults_for_timestamp) 系统变量的值

- 如果 [`explicit_defaults_for_timestamp`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_explicit_defaults_for_timestamp) 启用，则不会自动将`DEFAULT CURRENT_TIMESTAMP`或`ON UPDATE CURRENT_TIMESTAMP`属性分配 给任何 [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)列。它们必须明确包含在列定义中。此外，任何 [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)未明确声明为`NOT NULL`允许 `NULL`值。

- 如果 [`explicit_defaults_for_timestamp`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_explicit_defaults_for_timestamp) 禁用，服务器处理`TIMESTAMP` 如下：

  - 除非另有说明，否则[`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)表中的第一 列被定义为在未明确分配值的情况下自动设置为最近修改的日期和时间。这[`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html) 对于记录[`INSERT`](https://dev.mysql.com/doc/refman/8.0/en/insert.html)or [`UPDATE`](https://dev.mysql.com/doc/refman/8.0/en/update.html)操作的时间戳很有用 。您还可以[`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)通过为其分配一个`NULL`值将任何列设置为当前日期和时间 ，除非它已使用`NULL`属性定义为允许 `NULL`值。

  - 可以使用`DEFAULT CURRENT_TIMESTAMP`和`ON UPDATE CURRENT_TIMESTAMP`列定义子句指定自动初始化和更新到当前日期和时间。默认情况下，第一[`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html) 列具有这些属性，如前所述。但是，[`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)表中的任何列都可以定义为具有这些属性。

**创建表**

```mysql
CREATE TABLE test_timestamp (
	`created_at` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '日期时间类型',
    `updated_at` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '日期时间类型'
) ENGINE=INNODB CHARSET=utf8;
```

- DEFAULT CURRENT_TIMESTAMP 声明默认值为当前时间

- DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP 声明默认值为当前时间并且在行发生修改时该字段值自动更新为当前时间

`如果要把值默认为NULL，DEFAULT NULL这种方式不好使 要使用以下方式`

```mysql
CREATE TABLE ... (
	...
	`created_at` TIMESTAMP NULL COMMENT '',
    ...
) ...
```



**插入值**

```mysql
INSERT INTO test_timestamp (`created_at`) VALUE ('25:10:10');
INSERT INTO test_timestamp (`created_at`) VALUE ('10:10:10');
INSERT INTO test_timestamp (`created_at`) VALUE ('10:10:10 10:10:10');
INSERT INTO test_timestamp (`created_at`) VALUE (CURRENT_DATE);
INSERT INTO test_timestamp (`created_at`) VALUE (CURRENT_TIME);
INSERT INTO test_timestamp (`created_at`) VALUE (CURRENT_TIMESTAMP);
INSERT INTO test_timestamp (`created_at`) VALUE (NOW());

> SELECT * FROM test_timestamp;
created_at updated_at
2025-10-10 00:00:00	2021-11-03 14:19:43
2010-10-10 00:00:00	2021-11-03 14:19:43
2010-10-10 10:10:10	2021-11-03 14:19:43
2021-11-03 00:00:00	2021-11-03 14:19:43
2021-11-03 14:19:43	2021-11-03 14:19:43
2021-11-03 14:19:43	2021-11-03 14:19:43
2021-11-03 14:19:43	2021-11-03 14:19:43
```



# DATETIME VS TIMESTAMP

关于datetime和timestamp类型列值的自动插入和更新参考官方说明：https://dev.mysql.com/doc/refman/8.0/en/timestamp-initialization.html

**两种方式的区别**

第一：取值范围不同

第二：存储方式的不同

- TIMESTAMP的值，以客户端传入的值和其时区统一转换为UTC 进行存储，查询时从 UTC 转换回客户端当前时区以进行检索

- 而DATETIME的值只要在允许的范围内存进去是什么样取出来就是什么样

第三：占用存储空间不同

- TIMESTAMP占用4个字节
- DATETIME占用8个字节













