---
title: MySQL知识梳理-6.系统函数
abbrlink: dc1264ba
date: 2021-03-25 13:47:01
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218919853.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218919853.jpg'
---





Dual表是虚拟表，并没有实际意义，MySQL的中的Dual只是为了满足SQL的习惯，在MySQL中以下两种sql效果一致

```mysql
SELECT NOW();
SELECT NOW() FROM DUAL;
```

但是在Oracle中第一种写法会报错，必须使用第二种

但是MySQL中 `SELECT * FROM DUAL;` 会报错 `1096 - No tables used`



# 常用时间函数

## CURDATE() / CURTIME()

返回当前日期 / 返回当前时间

举例：

```mysql
-- 以下都返回 2021-03-25
SELECT CURDATE() FROM DUAL;
SELECT CURRENT_DATE FROM DUAL;

-- 以下都返回 14:09:48
SELECT CURTIME() FROM DUAL;
SELECT CURRENT_TIME FROM DUAL;
```



## NOW()

返回当前的日期和时间

举例：

```mysql
SELECT NOW() FROM DUAL;
-- 返回 2021-03-25 14:11:14
```



## DATE_FORMAT(date, fmt)

按照fmt格式对日期date进行格式化

fmt：

- %Y   四位的年
- %m 月份(00-12)
- %d  天(00-31) 
- %H 小时(00-24) 
- %i   分钟(00-59) 
-  %s  秒(00-59)

举例：

```mysql
-- 返回 2021--25--25 14:20:35
SELECT DATE_FORMAT(NOW(), '%Y--%d--%d %H:%i:%s') FROM DUAL;

-- 返回 20191111 00:00:00
SELECT DATE_FORMAT('2019-05-11', '%Y%d%d %H:%i:%s') FROM DUAL;
```



## SEC_TO_TIME(seconds)

把秒数转换为(小时:分:秒)

举例：

```mysql
-- 返回 01:40:00，即6000秒是1个小时40分钟
SELECT SEC_TO_TIME(6000) FROM DUAL;
```



## TIME_TO_SEC(H:i:s)

把时间(小时:分:秒)转换为秒数

举例：

```mysql
-- 返回 3000， 即50分钟为3000秒
SELECT TIME_TO_SEC('00:50:00') FROM DUAL;
```



## DATEDIFF(date1,date2)

返回date1和date2两个日期相差的天数

注意：第一个参数为靠后的日期，否则会出现负数

举例：

```mysql
-- 返回 731
SELECT DATEDIFF('2021-03-25', '2019-03-25') FROM DUAL;
```



## DATE_ADD(date,INTERVAL expr unit)

对给定的日期增加或减少指定的时间单元

unit：

- DAY    天
- HOUR  小时
- MINUTES    分钟
- SECOND  秒

举例：

```mysql
-- 当前日期时间  返回 2021-03-25 14:34:22
SELECT NOW() FROM DUAL;
-- 一天后  返回 2021-03-26 14:34:22
SELECT DATE_ADD(NOW(), INTERVAL 1 DAY) FROM DUAL;
-- 一年之前  返回 2020-03-25 14:34:22
SELECT DATE_ADD(NOW(), INTERVAL -1 YEAR) FROM DUAL;
-- 一个半小时之前 返回 2021-03-25 13:04:22
SELECT DATE_ADD(NOW() , INTERVAL '-1:30' HOUR_MINUTE) FROM DUAL;
```



## EXTRACT(unit FROM date)

返回日期date的指定部分

举例：

```mysql
-- 当前日期时间  返回 2021-03-25 14:37:39
SELECT NOW() FROM DUAL;
-- 年份  返回 2021
SELECT EXTRACT(YEAR FROM NOW()) FROM DUAL;
-- 日期  25
SELECT EXTRACT(DAY FROM NOW()) FROM DUAL;
```





## UNIX_TIMESTAMP()

返回unix时间戳

举例：

```mysql
-- 返回当前秒级时间戳
SELECT UNIX_TIMESTAMP() FROM DUAL;

-- 返回 2020-01-01 5:04:45 的时间戳
SELECT UNIX_TIMESTAMP('2020-01-01 5:04:45') FROM DUAL;
```





## FROM_UNIXTIME()

把UNIX时间戳转换为日期时间

举例：

```mysql
-- 返回 1577826285的日期时间 2020-01-01 05:04:45
SELECT FROM_UNIXTIME(1577826285) FROM DUAL;

-- 返回 1577826285的日期时间并进行格式化 2020--01--01 05:04:45
SELECT FROM_UNIXTIME(1577826285, '%Y--%m--%d %H:%i:%s') FROM DUAL;
```





# 常用字符串函数

## CONCAT(str1, str2...)

把多个字符串连接成一个字符串

举例：

```mysql
-- 拼接aa和bb  返回aabb
SELECT CONCAT('aa','bb') FROM DUAL;
```



## CONCAT_WS(sep, str1, str2...)

用指定的分隔符sep连接字符串

举例：

```mysql
使用-字符拼接aa和bb  返回aa-bb
SELECT CONCAT_WS('-', 'aa','bb') FROM DUAL;
```



## CHAR_LENGTH(str)

返回字符串str的字符个数

举例：

```mysql
-- 返回3  只有3个字符
SELECT CHAR_LENGTH('aa赵') FROM DUAL;
```



## LENGTH(str)

返回字符串str的字节个数

举例：

```mysql
-- 返回5   一个中文占3个字节
SELECT LENGTH('aa赵') FROM DUAL;
```



## FORMAT(X, D[,locale])

将数字X格式化，如 "#,###,###.##"， 并舍入到D位小数

- `locale`是一个可选参数，用于确定千个分隔符和分隔符之间的分组。如果省略`locale`操作符，MySQL将默认使用`en_US`。以下链接提供MySQL支持的所有区域名称：http://dev.mysql.com/doc/refman/5.7/en/locale-support.html

举例：

```mysql
-- 返回 10,000.00
SELECT FORMAT(10000,2) FROM DUAL;
```



## LEFT(str,len) / RIGHT(str,len)

  从字符串左/右 起返回长度为len的子字符串

举例：

```mysql
-- 返回bbbbb和aaaa
SELECT LEFT('bbbbbb', 5),RIGHT('aaaa', 4) FROM DUAL;
```



## SUBSTRING(str, pos[, len])

从字符串str的pos位置起返回长度为len的子串，如果未指定len则一直到字符串结束

举例：

```mysql
-- 返回aa和aaaa
SELECT SUBSTRING('aaaaaa' FROM 3 FOR 2),SUBSTRING('aaaaaa' FROM 3) FROM DUAL;
```



## SUBSTRING_INDEX(str, delim,count)

返回字符串str按delim分割的前count个子字符串，从右边的第count应该为负数

举例：

```mysql
-- 返回aa-bb
SELECT SUBSTRING_INDEX('aa-bb-cc-dd-ee','-',2) FROM DUAL;
```



## LOCATE(substr, str[,pos])

返回substr在str中第一次出现的位置

pos表示从第几个位置开始

举例：

```mysql
-- 返回3和6
SELECT LOCATE('-','aa-bb-cc-dd-ee'), LOCATE('-', 'aa-bb-cc-dd-ee', 4) FROM DUAL;
```



## TRIM([remstr FROM] str)

在字符串str两端删除不需要的字符remstr，不指定remstr则默认删除空格

举例：

```mysql
-- 返回aa bb和aa-bb
SELECT TRIM(' aa bb '),TRIM('-' FROM '-aa-bb-') FROM DUAL;
```



# 其他常用函数

## RAOUD(X[,D])

对数值x进行四舍五入保留D位小数

举例：

```mysql
-- 返回3和3.142
SELECT ROUND(3.1415926), ROUND(3.1415926, 3) FROM DUAL;
```



## RAND()

返回一个在0-1之间的随机数

举例：

```mysql
-- 每次执行返回结果都可能不一样
SELECT RAND() FROM DUAL;
```



## 流程控制

语法格式

```
CASE WHEN [condition] 
	THEN result 
	[WHEN [condition] THEN result ...] 
	[ELSE result] END
```



## MD5(str)

返回str的MD5值

举例：

```mysql

```







# 窗口函数

MySQL8.0版本新功能。

## 介绍

简单来说就是`对于一个查询SQL，将其结果集按指定的规则进行分区，每个分区可以看作是一个窗口，分区内的每一行，根据其所属分区内的行数据进行函数计算，获取计算结果，作为该行的窗口函数结果值`。

窗口函数与group聚合查询类似，都是对一组（分区）记录进行计算，区别在于`group对一组记录计算后返回一条记录作为结果`，而`窗口函数对一组记录计算后，这组记录中每条数据都会对应一个结果`。

`窗口函数是对 where 和 group by子句处理后的结果进行操作，因此按照SQL语句的运行顺序，窗口函数一般放在select子句中`

在满足某些条件的记录集合上执行的特殊函数，对于每条记录都要在此窗口内执行函数。有的函数随着记录的不同，窗口大小都是固定的，称为`静态窗口`；有的函数则相反，不同的记录对应着不同的窗口，称为`滑动窗口`



## 作用

- 解决排名问题，e.g. 每个班级按成绩排名
- 解决TOPN问题，e.g. 每个班级前两名的学生
- 组内比较问题（聚合函数）
- 移动平均



## 基本语法

```
{OVER (window_spec) | OVER window_name}
```

两种格式，区别在于窗口是直接在 OVER 子句中定义，还是由对查询中其他位置定义的命名窗口的引用提供

第一种格式，窗口直接出现在 OVER 括号之间的子句中，比如 

```mysql
SELECT
    ...
    ROW_NUMBER() OVER(PARTITION BY country ORDER BY year, product) AS row_num1
FROM sales;
```

第二种情况，window_name 是由 WINDOW查询中其他子句定义的窗口规范，称为 `命名窗口`

```mysql
SELECT
  val,
  ROW_NUMBER() OVER (ORDER BY val) AS 'row_number',
  RANK()       OVER (ORDER BY val) AS 'rank',
  DENSE_RANK() OVER (ORDER BY val) AS 'dense_rank'
FROM numbers;
-- 以上这种每个窗口写法都是一样的 就可以写成下面这种
-- 类似于对相同的窗口内容做了一次封装，在多个窗口中调用
SELECT
  val,
  ROW_NUMBER() OVER w AS 'row_number',
  RANK()       OVER w AS 'rank',
  DENSE_RANK() OVER w AS 'dense_rank'
FROM numbers
WINDOW w AS (ORDER BY val);
```

window_spec

```
[window_name] [partition_clause] [order_clause] [frame_clause]
```

如果 window_spec 为空，则该窗口由所有查询行组成，并且window函数使用所有行来计算结果。否则，括号中的子句将确定哪些查询行用于计算函数结果以及它们的划分和排序方式

- window_name（针对命名窗口）

  由 WINDOW 查询中其他子句定义的窗口的名称。如果 window_name 它本身出现在 OVER 子句中，则它将完全定义该窗口。如果还给出了分区，排序或框架子句，则它们会修改对命名窗口的解释

  ```mysql
  -- 以下执行名称为w的窗口函数
  SELECT
      ...
      ROW_NUMBER() OVER w AS row_num1
  FROM sales
  WINDOW w AS (ORDER BY val);
  -- 以下 OVER 定义的ORDER BY 会覆盖名称为w窗口中的ORDER BY
  SELECT
      ...
      ROW_NUMBER() OVER (w ORDER BY val) AS row_num1
  FROM sales
  WINDOW w AS (PARTITION BY country);
  ```

  注意：

  1. 一个 OVER 子句只能添加属性命名的窗口，不能修改它们。`如果命名的窗口定义包含分区，排序或框架属性，则 OVER 引用窗口名称的 子句不能包含相同类型的属性`，否则会发生错误，如下会报错

     ```mysql
     SELECT
         ...
         ROW_NUMBER() OVER (w ORDER BY val) AS row_num1
     FROM sales
     WINDOW w AS (PARTITION BY country ORDER BY year, product);
     ```

  2. 命名窗口的定义本身可以以开头 *`window_name`*。在这种情况下，允许向前和向后引用，但不允许循环

     ```mysql
     -- 这是正常的
     WINDOW w1 AS (w2), w2 AS (), w3 AS (w1)
     -- 这是错误的
     WINDOW w1 AS (w2), w2 AS (w3), w3 AS (w1)
     ```

- partition_clause

  ```
  PARTITION BY expr [, expr] ...
  ```

  PARTITION BY子句指示如何将查询行划分为组。给定行的窗口函数结果基于包含该行分区的行。如果省略PARTITION BY，则只存在一个由所有查询行组成的分区。

  标准SQL仅支持 PARTITION BY 后跟列名。MySQL允许表达式，而不仅仅是列名。例如，如果表包含 [`TIMESTAMP`](https://dev.mysql.com/doc/refman/8.0/en/datetime.html) 名为的列`ts`，则标准SQL允许， PARTITION BY ts 但不允许 PARTITION BY HOUR(ts)，而MySQL两者都允许。

- order_clause

  ```
  ORDER BY expr [ASC|DESC] [, expr [ASC|DESC]] ...
  ```

  ORDER BY子句指示如何对每个分区中的行进行排序。根据ORDER BY子句相等的分区行被认为是对等的。如果省略ORDER BY，则分区行是无序的，没有隐含的处理顺序，并且所有分区行都是对等的。

  每个ORDER BY表达式后面都可以有ASC或DESC来指示排序方向。如果没有指定方向，默认为ASC。对于升序排序，空值优先排序，对于降序排序，空值最后排序。

  窗口定义中的ORDER BY适用于各个分区。要对结果集进行整体排序，请在查询顶层包含ORDER BY。

- frame_clause

  框架是当前分区的子集，框架子句指定如何定义这个子集。框架子句有许多自己的子句。请参见 [第12.21.3节“窗口功能框架规范”](https://dev.mysql.com/doc/refman/8.0/en/window-functions-frames.html)

  `未完待定`



## 使用

可用于窗口函数的聚合函数

| 函数                          | 说明                         |
| ----------------------------- | ---------------------------- |
| AVG()                         | 返回参数的平均值             |
| BIT_AND()                     | 按位返回AND                  |
| BIT_OR()                      | 按位返回OR                   |
| BIT_XOR()                     | 返回按位异或                 |
| COUNT()                       | 返回计数返回的行数           |
| JSON_ARRAYAGG()               | 将结果集作为单个JSON数组返回 |
| JSON_OBJECTAGG()              | 将结果集作为单个JSON对象返回 |
| MAX() MIN()                   | 返回最大/最小值              |
| STDDEV_POP(), STDDEV(), STD() | 返回总体标准差               |
| STDDEV_SAMP()                 | 返回样本标准差               |
| SUM()                         | 返回总和                     |
| VAR_POP(), VARIANCE()         | 返回总体标准方差             |
| VAR_SAMP()                    | 返回样本方差                 |



可用于窗口函数的非聚合函数

| 函数名         | 说明                                                        |
| -------------- | ----------------------------------------------------------- |
| CUME_DIST()    | 累积分布值                                                  |
| ROW_NUMBER()   | 分区内的当前行数，也可当做顺序排序的序列号，连续不中断      |
| RANK()         | 当前行在其分区中的名次，带有间隙，跳过重复序号，比如 1 1 3  |
| DENSE_RANK()   | 当前行在其分区内的名次，无间隙，不跳过重复序号， 比如 1 1 2 |
| FIRST_VALUE()  | 窗口框架第一行的参数值                                      |
| LAG()          | 分区内当前行 前N行 的参数值                                 |
| LAST_VALUE()   | 窗口框架最后一行的参数值                                    |
| LEAD()         | 分区内当前行的前导参数的值                                  |
| NTH_VALUE()    | 从窗口第n行开始的参数值                                     |
| NTILE()        | Bucket分区内当前行数。                                      |
| PERCENT_RANK() | 排名百分比值                                                |

