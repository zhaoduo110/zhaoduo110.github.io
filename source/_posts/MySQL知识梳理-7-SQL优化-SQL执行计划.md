---
title: MySQL知识梳理-7.SQL优化-SQL执行计划
abbrlink: cbf672b7
date: 2021-03-27 14:19:49
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
---





# 关注SQL执行计划的好处

- 了解SQL如何访问表中的数据
- 了解SQL如何使用表中的索引
- 了解SQL所使用的查询类型
- 总之就是为了清楚SQL的执行信息，便于找到问题进行优化



# 了解SQL执行计划

其实就是 EXPLAIN 关键字，在要了解执行计划的sql之前加上 EXPLAIN 即可，将返回来自优化器的有关语句执行计划的信息

## 语法格式

```
{EXPLAIN | DESCRIBE | DESC}
    tbl_name [col_name | wild]

{EXPLAIN | DESCRIBE | DESC}
    [explain_type]
    {explainable_stmt | FOR CONNECTION connection_id}

explain_type: {
    FORMAT = format_name
}

format_name: {
    TRADITIONAL
  | JSON
}

explainable_stmt: {
    SELECT statement
  | DELETE statement
  | INSERT statement
  | REPLACE statement
  | UPDATE statement
}
```

EXPLAIN不仅可以分析sql执行计划，还可以对表和表字段使用，对表使用时效果和DESCRIBE、DESC一样，返回表结构信息

EXPLAIN还可以根据connection_id对正在执行的sql进行分析

MySQL 8.0.18引入`EXPLAIN ANALYZE` + SQL 可以显示估计执行成本、预计返回的行数、返回第一行时间、返回所有行的时间（实际成本），以毫秒为单位、迭代器返回的行数、循环数等信息



## 结果

<img src="https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20210327151111.png" style="zoom:150%;" />



各字段含义说明

**id**

- 每个SQL执顺序的标识, SQL从大到小的执行（）
- id相同时，执行顺序由上至下
- 如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行
- id如果相同，可以认为是一组，从上往下顺序执行；在所有组中，id值越大，优先级越高，越先执行

**select_type：查询中每个select子句的类型，可能出现的值及其含义**

- SIMPLE：简单SELECT,不使用UNION或子查询等
- PRIMARY：查询中若包含任何复杂的子部分,最外层的select被标记为PRIMARY
- UNION：UNION中的第二个或后面的SELECT语句
- DEPENDENT UNION：UNION中的第二个或后面的SELECT语句，取决于外面的查询
- UNION RESULT：UNION的结果
- SUBQUERY：子查询中的第一个SELECT
- DEPENDENT SUBQUERY：子查询中的第一个SELECT，取决于外面的查询
- DERIVED：派生表的SELECT, FROM子句的子查询
- UNCACHEABLE SUBQUERY：一个子查询的结果不能被缓存，必须重新评估外链接的第一行

**table：作用于哪张数据表**

- 不一定是真实的表名字，如果依赖于上一步执行则看到的是derivedx（x应该是上一步执行的id）

**type：查询数据的方式**

| 性能 | type值 | 说明                                                         |
| ---- | ------ | ------------------------------------------------------------ |
| 差   | ALL    | Full Table Scan， MySQL将遍历全表以找到匹配的行              |
| \|   | index  | Full Index Scan，index与ALL区别为index类型只遍历索引树       |
| \|   | range  | 只检索给定范围的行，使用一个索引来选择行，一般作用于 BETWEEN < > in这种区间判断条件 |
| \|   | ref    | 表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值 |
| \|   | eq_ref | 类似ref，区别就在使用的索引是唯一索引，对于每个索引键值，表中只有一条记录匹配，简单来说。<br>就是多表连接中使用primary key或者 unique key作为关联条件 |
| \|   | const  | 当MySQL对查询某部分进行优化，并转换为一个常量时，使用这些类型访问。<br>如将主键置于where列表中，MySQL就能将该查询转换为一个常量 |
| V    | system | system是const类型的特例，当查询的表只有一行的情况下，使用system |
| 好   | NULL   | MySQL在优化过程中分解语句，执行时甚至不用访问表或索引，例如从一个索引列里选取最小值可以通过单独索引查找完成 |

**possible_keys：可能使用到的索引**

- 指出MySQL能使用哪个索引在表中找到记录，查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询使用
- 该列完全独立于EXPLAIN输出所示的表的次序。这意味着在possible_keys中的某些键实际上不能按生成的表次序使用。
- 如果该列是NULL，则没有相关的索引。在这种情况下，可以通过检查WHERE子句看是否它引用某些列或适合索引的列来提高你的查询性能。如果是这样，创造一个适当的索引并且再次用EXPLAIN检查查询

**Key：使用使用的索引**

- 如果没有选择索引，键是NULL。要想强制MySQL使用或忽视possible_keys列中的索引，在查询中使用FORCE INDEX、USE INDEX或者IGNORE INDEX

**key_len：索引中使用的字节数**

- 可通过该列计算查询中使用的索引的长度
- key_len显示的值为索引字段的最大可能长度，并非实际使用长度，即key_len是根据表定义计算而得，不是通过表内检索出的

- 不损失精确性的情况下，长度越短越好 

**ref**

表示上述表的连接匹配条件，即哪些列或常量被用于查找索引列上的值

**rows：可能需要扫描的行数**

表示MySQL根据表统计信息及索引选用情况，估算的找到所需的记录所需要读取的行数，越少越好

**Extra**

| 值                           | 说明                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| Using where                  | 列数据是从仅仅使用了索引中的信息而没有读取实际的行动的表返回的，<br>这发生在对表的全部的请求列都是同一个索引的部分的时候，表示mysql服务器将在存储引擎检索行后再进行过滤 |
| Using temporary              | 表示MySQL需要使用临时表来存储结果集，常见于排序和分组查询    |
| Using filesort               | MySQL中无法利用索引完成的排序操作称为“文件排序”              |
| Using index                  | 使用了覆盖索引进行查询                                       |
| Using join buffer            | 强调了在获取连接条件时没有使用索引，并且需要连接缓冲区来存储中间结果。<br>`如果出现了这个值，那应该注意，根据查询的具体情况可能需要添加索引来改进能` |
| Impossible where             | 强调了where语句会导致没有符合条件的行，where子句的值总是false，不能用来获取任何元组 |
| Select tables optimized away | 这个值意味着仅通过使用索引而不用访问表，优化器可能仅从聚合函数结果中返回一行 |
| Distinct                     | 优化distinct操作，在找到第一匹配的元组后即停止找同样值的动作 |
| Not exists                   | 使用 not exists来优化查询                                    |



## 注意：

- EXPLAIN不会告诉你关于触发器、存储过程的信息或用户自定义函数对查询的影响情况
- EXPLAIN不考虑各种Cache
- EXPLAIN不能显示MySQL在执行查询时所作的优化工作
- 部分统计信息是估算的，并非精确值
- EXPALIN只能解释SELECT操作，其他操作要重写为SELECT后查看执行计划



# 根据SQL执行计划有针对性优化SQL



##SQL改写优化

- 使用 OUTER JOIN 代替NOT IN
- 使用 CTE 代替子查询
- 拆分复杂的大SQL ，尽量使用小的SQL
- 使用计算列查询



## [索引优化](./eea2a833.html)





