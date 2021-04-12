---
title: MySQL知识梳理-5.SQL语言-DDL
abbrlink: a00529d2
date: 2021-03-23 17:06:16
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
---





# SQL语句

一种描述性语言

## 作用

对存储在RDBMS中的数据进行增删改查等操作



## 分类

**数据控制语言**：DCL（Data Control Language）`GRANT,REVOKE,COMMIT,ROLLBACK` 对数据库安全性完整性等有操作的，可以简单的理解为权限控制或者事务操作等

**数据定义语言**：DDL（Data Ddefinition Language) `CREATE,DROP,ALTER` 主要为以上操作 即对逻辑结构等有操作的，其中包括表结构，视图和索引。

**数据查询语言**：DQL（Data Query Language）`SELECT` 查询操作，以select关键字。各种简单查询，连接查询等 都属于DQL。

**数据操纵语言**：DML（Data Manipulation Language）`INSERT,UPDATE,DELETE` 对数据进行操作的，对应上面所说的查询操作 DQL与DML共同构建了多数初级程序员常用的增删改查操作。而查询是较为特殊的一种 被划分到DQL中。



## 官方文档

https://dev.mysql.com/doc/refman/8.0/en/sql-statements.html



# DDL



## DATABASE：数据库

### 创建

CREATE DATABASE

**语法格式**

```sql
CREATE {DATABASE | SCHEMA} [IF NOT EXISTS] db_name
    [create_option] ...
```

create_option

```
create_option: [DEFAULT] {
    CHARACTER SET [=] charset_name
  | COLLATE [=] collation_name
  | ENCRYPTION [=] {'Y' | 'N'}
}
```

- CHARACTER SET 字符集

  `SHOW CHARACTER SET;` 命令可查看所有支持的字符集

- COLLATE 检验规则

  `SHOW COLLATION;` 命令可查看所有的检验规则

- ENCRYPTION

  该选项在MySQL 8.0.16中引入，定义了默认的数据库加密，该默认加密由数据库中创建的表继承。允许的值为`'Y'`（启用加密）和 `'N'`（禁用加密）。如果`ENCRYPTION`未指定该 选项，则[`default_table_encryption`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_table_encryption) 系统变量的值 定义默认的数据库加密。如果[`table_encryption_privilege_check`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_table_encryption_privilege_check) 启用了 系统变量， [`TABLE_ENCRYPTION_ADMIN`](https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#priv_table-encryption-admin) 则需要特权来指定不同于该[`default_table_encryption`](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_table_encryption) 设置的默认加密 设置。有关更多信息，请参见 [为架构和常规表空间定义加密默认值](https://dev.mysql.com/doc/refman/8.0/en/innodb-data-encryption.html#innodb-schema-tablespace-encryption-default)



**注意事项**

- 如果未指定IF NOT EXISTS 并且给定的数据库名称已存在的话则会报错
- MySQL对创建的数据库数量没有限制，`但基础文件系统可能对目录数量有所限制`
- 数据库名称命名规则：[第9.2节“架构对象名称”](https://dev.mysql.com/doc/refman/8.0/en/identifiers.html)
- CREATE DATABASE 和 CREATE SCHEMA 一样效果



字符集相关信息官方文档： [第10章，*字符集，归类，Unicode*](https://dev.mysql.com/doc/refman/8.0/en/charset.html)



### 修改

ALTER DATABASE

语法格式

```
ALTER {DATABASE | SCHEMA} [db_name]
    alter_option ...
```

alter_option

```
alter_option: {
    [DEFAULT] CHARACTER SET [=] charset_name
  | [DEFAULT] COLLATE [=] collation_name
  | [DEFAULT] ENCRYPTION [=] {'Y' | 'N'}
  | READ ONLY [=] {DEFAULT | 0 | 1}
}
```

Read Only Option

详解见官方文档：https://dev.mysql.com/doc/refman/8.0/en/alter-database.html

- 在MySQL 8.0.22中引入，控制是否允许修改数据库及其中的对象。允许的值是 `DEFAULT`或`0`（非只读）和`1`（只读）。
- 此选项对数据库迁移很有用，因为`READ ONLY`可以将已启用数据库的数据库迁移到另一个MySQL实例，而不必担心在操作过程中可能会更改该数据库
- 使用NDB群集，使数据库只能在一台[**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html)服务器上只读 ，并与同一群集中的其他[**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html)服务器同步 ，以便该数据库在所有[**mysqld**](https://dev.mysql.com/doc/refman/8.0/en/mysqld.html) 服务器上变为只读



**注意事项**

- 如果省略了db_name，则语句会作用于默认数据库，如果没有默认数据库会报错
- 对于ALTER语句中未明确指定修改的选项将保留当前选项值，但对于修改了CHARACTER SET的，COLLATE也会发生修改，反之亦然
- 在创建存储过程时如果未明确指定字符集或排序规则，则字符串类型的变量会使用数据库默认值，`那么如果修改了数据库的字符集和校验规则也必须把要使用新默认值得所有存储过程删除再重建`。这里的意思大致是存储过程使用字符集和校验规则的默认值是复制数据库的，而不是指向引用
- 如果修改了默认的数据库加密，只有新创建的表才能继承。对于与数据库关联的现有表，其加密保持不变





### 删除

DROP DATABASE

语法格式

```sql
DROP {DATABASE | SCHEMA} [IF EXISTS] db_name
```

- 删除数据库中的所有表并删除数据库，要 *非常*小心！
- 删除数据库时，*不会*自动删除专门为该数据库授予的特权。必须手动删除它们
- `IF EXISTS` 用于防止数据库不存在时发生错误
- 如果删除了默认数据库，则默认数据库不可用（[`DATABASE()`](https://dev.mysql.com/doc/refman/8.0/en/information-functions.html#function_database)函数会返回  NULL）
- 如果该命令在符号链接的数据库上使用，则链接和原始数据库都将被删除
- 返回信息为已删除的表数
- 该语句从给定的数据库目录中删除MySQL本身在正常操作期间可能创建的那些文件和目录。这包括具有以下列表中所示扩展名的所有文件：
  - `.BAK`
  - `.DAT`
  - `.HSH`
  - `.MRG`
  - `.MYD`
  - `.MYI`
  - `.cfg`
  - `.db`
  - `.ibd`
  - `.ndb`
- 如果在MySQL删除刚列出的文件或目录后，其他文件或目录仍保留在数据库目录中，则无法删除数据库目录。在这种情况下，您必须手动删除所有剩余的文件或目录，然后再次执行该语句
- 删除数据库不会删除`TEMPORARY`在该数据库中创建的任何表。`TEMPORARY`创建表的会话结束时，将自动删除这些表



## TABLE：数据表

### 创建

CREATE TABLE 

语法格式

```sql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    (create_definition,...)
    [table_options]
    [partition_options]

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    [(create_definition,...)]
    [table_options]
    [partition_options]
    [IGNORE | REPLACE]
    [AS] query_expression

CREATE [TEMPORARY] TABLE [IF NOT EXISTS] tbl_name
    { LIKE old_tbl_name | (LIKE old_tbl_name) }
```



各种属性的定义和取值，详细请参考[官方文档](https://dev.mysql.com/doc/refman/8.0/en/create-table.html)

```
create_definition: {
    col_name column_definition
  | {INDEX | KEY} [index_name] [index_type] (key_part,...)
      [index_option] ...
  | {FULLTEXT | SPATIAL} [INDEX | KEY] [index_name] (key_part,...)
      [index_option] ...
  | [CONSTRAINT [symbol]] PRIMARY KEY
      [index_type] (key_part,...)
      [index_option] ...
  | [CONSTRAINT [symbol]] UNIQUE [INDEX | KEY]
      [index_name] [index_type] (key_part,...)
      [index_option] ...
  | [CONSTRAINT [symbol]] FOREIGN KEY
      [index_name] (col_name,...)
      reference_definition
  | check_constraint_definition
}

column_definition: {
    data_type [NOT NULL | NULL] [DEFAULT {literal | (expr)} ]
      [VISIBLE | INVISIBLE]
      [AUTO_INCREMENT] [UNIQUE [KEY]] [[PRIMARY] KEY]
      [COMMENT 'string']
      [COLLATE collation_name]
      [COLUMN_FORMAT {FIXED | DYNAMIC | DEFAULT}]
      [ENGINE_ATTRIBUTE [=] 'string']
      [SECONDARY_ENGINE_ATTRIBUTE [=] 'string']
      [STORAGE {DISK | MEMORY}]
      [reference_definition]
      [check_constraint_definition]
  | data_type
      [COLLATE collation_name]
      [GENERATED ALWAYS] AS (expr)
      [VIRTUAL | STORED] [NOT NULL | NULL]
      [VISIBLE | INVISIBLE]
      [UNIQUE [KEY]] [[PRIMARY] KEY]
      [COMMENT 'string']
      [reference_definition]
      [check_constraint_definition]
}

data_type:
    (see Chapter 11, Data Types)

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_type:
    USING {BTREE | HASH}

index_option: {
    KEY_BLOCK_SIZE [=] value
  | index_type
  | WITH PARSER parser_name
  | COMMENT 'string'
  | {VISIBLE | INVISIBLE}
  |ENGINE_ATTRIBUTE [=] 'string'
  |SECONDARY_ENGINE_ATTRIBUTE [=] 'string'
}

check_constraint_definition:
    [CONSTRAINT [symbol]] CHECK (expr) [[NOT] ENFORCED]

reference_definition:
    REFERENCES tbl_name (key_part,...)
      [MATCH FULL | MATCH PARTIAL | MATCH SIMPLE]
      [ON DELETE reference_option]
      [ON UPDATE reference_option]

reference_option:
    RESTRICT | CASCADE | SET NULL | NO ACTION | SET DEFAULT

table_options:
    table_option [[,] table_option] ...

table_option: {
    AUTOEXTEND_SIZE [=] value
  | AUTO_INCREMENT [=] value
  | AVG_ROW_LENGTH [=] value
  | [DEFAULT] CHARACTER SET [=] charset_name
  | CHECKSUM [=] {0 | 1}
  | [DEFAULT] COLLATE [=] collation_name
  | COMMENT [=] 'string'
  | COMPRESSION [=] {'ZLIB' | 'LZ4' | 'NONE'}
  | CONNECTION [=] 'connect_string'
  | {DATA | INDEX} DIRECTORY [=] 'absolute path to directory'
  | DELAY_KEY_WRITE [=] {0 | 1}
  | ENCRYPTION [=] {'Y' | 'N'}
  | ENGINE [=] engine_name
  | ENGINE_ATTRIBUTE [=] 'string'
  | INSERT_METHOD [=] { NO | FIRST | LAST }
  | KEY_BLOCK_SIZE [=] value
  | MAX_ROWS [=] value
  | MIN_ROWS [=] value
  | PACK_KEYS [=] {0 | 1 | DEFAULT}
  | PASSWORD [=] 'string'
  | ROW_FORMAT [=] {DEFAULT | DYNAMIC | FIXED | COMPRESSED | REDUNDANT | COMPACT}
  | SECONDARY_ENGINE_ATTRIBUTE [=] 'string'
  | STATS_AUTO_RECALC [=] {DEFAULT | 0 | 1}
  | STATS_PERSISTENT [=] {DEFAULT | 0 | 1}
  | STATS_SAMPLE_PAGES [=] value
  | TABLESPACE tablespace_name [STORAGE {DISK | MEMORY}]
  | UNION [=] (tbl_name[,tbl_name]...)
}

partition_options:
    PARTITION BY
        { [LINEAR] HASH(expr)
        | [LINEAR] KEY [ALGORITHM={1 | 2}] (column_list)
        | RANGE{(expr) | COLUMNS(column_list)}
        | LIST{(expr) | COLUMNS(column_list)} }
    [PARTITIONS num]
    [SUBPARTITION BY
        { [LINEAR] HASH(expr)
        | [LINEAR] KEY [ALGORITHM={1 | 2}] (column_list) }
      [SUBPARTITIONS num]
    ]
    [(partition_definition [, partition_definition] ...)]

partition_definition:
    PARTITION partition_name
        [VALUES
            {LESS THAN {(expr | value_list) | MAXVALUE}
            |
            IN (value_list)}]
        [[STORAGE] ENGINE [=] engine_name]
        [COMMENT [=] 'string' ]
        [DATA DIRECTORY [=] 'data_dir']
        [INDEX DIRECTORY [=] 'index_dir']
        [MAX_ROWS [=] max_number_of_rows]
        [MIN_ROWS [=] min_number_of_rows]
        [TABLESPACE [=] tablespace_name]
        [(subpartition_definition [, subpartition_definition] ...)]

subpartition_definition:
    SUBPARTITION logical_name
        [[STORAGE] ENGINE [=] engine_name]
        [COMMENT [=] 'string' ]
        [DATA DIRECTORY [=] 'data_dir']
        [INDEX DIRECTORY [=] 'index_dir']
        [MAX_ROWS [=] max_number_of_rows]
        [MIN_ROWS [=] min_number_of_rows]
        [TABLESPACE [=] tablespace_name]

query_expression:
    SELECT ...   (Some valid select or union statement)
```









### 修改

ALTER TABLE

语法格式

```sql
ALTER TABLE tbl_name
    [alter_option [, alter_option] ...]
    [partition_options]
```

各种属性的定义和取值，详细请参考[官方文档](https://dev.mysql.com/doc/refman/8.0/en/alter-table.html)

```

alter_option: {
    table_options
  | ADD [COLUMN] col_name column_definition
        [FIRST | AFTER col_name]
  | ADD [COLUMN] (col_name column_definition,...)
  | ADD {INDEX | KEY} [index_name]
        [index_type] (key_part,...) [index_option] ...
  | ADD {FULLTEXT | SPATIAL} [INDEX | KEY] [index_name]
        (key_part,...) [index_option] ...
  | ADD [CONSTRAINT [symbol]] PRIMARY KEY
        [index_type] (key_part,...)
        [index_option] ...
  | ADD [CONSTRAINT [symbol]] UNIQUE [INDEX | KEY]
        [index_name] [index_type] (key_part,...)
        [index_option] ...
  | ADD [CONSTRAINT [symbol]] FOREIGN KEY
        [index_name] (col_name,...)
        reference_definition
  | ADD [CONSTRAINT [symbol]] CHECK (expr) [[NOT] ENFORCED]
  | DROP {CHECK | CONSTRAINT} symbol
  | ALTER {CHECK | CONSTRAINT} symbol [NOT] ENFORCED
  | ALGORITHM [=] {DEFAULT | INSTANT | INPLACE | COPY}
  | ALTER [COLUMN] col_name {
        SET DEFAULT {literal | (expr)}
      | SET {VISIBLE | INVISIBLE}
      | DROP DEFAULT
    }
  | ALTER INDEX index_name {VISIBLE | INVISIBLE}
  | CHANGE [COLUMN] old_col_name new_col_name column_definition
        [FIRST | AFTER col_name]
  | [DEFAULT] CHARACTER SET [=] charset_name [COLLATE [=] collation_name]
  | CONVERT TO CHARACTER SET charset_name [COLLATE collation_name]
  | {DISABLE | ENABLE} KEYS
  | {DISCARD | IMPORT} TABLESPACE
  | DROP [COLUMN] col_name
  | DROP {INDEX | KEY} index_name
  | DROP PRIMARY KEY
  | DROP FOREIGN KEY fk_symbol
  | FORCE
  | LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
  | MODIFY [COLUMN] col_name column_definition
        [FIRST | AFTER col_name]
  | ORDER BY col_name [, col_name] ...
  | RENAME COLUMN old_col_name TO new_col_name
  | RENAME {INDEX | KEY} old_index_name TO new_index_name
  | RENAME [TO | AS] new_tbl_name
  | {WITHOUT | WITH} VALIDATION
}

partition_options:
    partition_option [partition_option] ...

partition_option: {
    ADD PARTITION (partition_definition)
  | DROP PARTITION partition_names
  | DISCARD PARTITION {partition_names | ALL} TABLESPACE
  | IMPORT PARTITION {partition_names | ALL} TABLESPACE
  | TRUNCATE PARTITION {partition_names | ALL}
  | COALESCE PARTITION number
  | REORGANIZE PARTITION partition_names INTO (partition_definitions)
  | EXCHANGE PARTITION partition_name WITH TABLE tbl_name [{WITH | WITHOUT} VALIDATION]
  | ANALYZE PARTITION {partition_names | ALL}
  | CHECK PARTITION {partition_names | ALL}
  | OPTIMIZE PARTITION {partition_names | ALL}
  | REBUILD PARTITION {partition_names | ALL}
  | REPAIR PARTITION {partition_names | ALL}
  | REMOVE PARTITIONING
}

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_type:
    USING {BTREE | HASH}

index_option: {
    KEY_BLOCK_SIZE [=] value
  | index_type
  | WITH PARSER parser_name
  | COMMENT 'string'
  | {VISIBLE | INVISIBLE}
}

table_options:
    table_option [[,] table_option] ...

table_option: {
    AUTOEXTEND_SIZE [=] value
  | AUTO_INCREMENT [=] value
  | AVG_ROW_LENGTH [=] value
  | [DEFAULT] CHARACTER SET [=] charset_name
  | CHECKSUM [=] {0 | 1}
  | [DEFAULT] COLLATE [=] collation_name
  | COMMENT [=] 'string'
  | COMPRESSION [=] {'ZLIB' | 'LZ4' | 'NONE'}
  | CONNECTION [=] 'connect_string'
  | {DATA | INDEX} DIRECTORY [=] 'absolute path to directory'
  | DELAY_KEY_WRITE [=] {0 | 1}
  | ENCRYPTION [=] {'Y' | 'N'}
  | ENGINE [=] engine_name
  | ENGINE_ATTRIBUTE [=] 'string'
  | INSERT_METHOD [=] { NO | FIRST | LAST }
  | KEY_BLOCK_SIZE [=] value
  | MAX_ROWS [=] value
  | MIN_ROWS [=] value
  | PACK_KEYS [=] {0 | 1 | DEFAULT}
  | PASSWORD [=] 'string'
  | ROW_FORMAT [=] {DEFAULT | DYNAMIC | FIXED | COMPRESSED | REDUNDANT | COMPACT}
  | SECONDARY_ENGINE_ATTRIBUTE [=] 'string'
  | STATS_AUTO_RECALC [=] {DEFAULT | 0 | 1}
  | STATS_PERSISTENT [=] {DEFAULT | 0 | 1}
  | STATS_SAMPLE_PAGES [=] value
  | TABLESPACE tablespace_name [STORAGE {DISK | MEMORY}]
  | UNION [=] (tbl_name[,tbl_name]...)
}

partition_options:
    (see CREATE TABLE options)
```







### 删除

DROP TABLE

语法格式

```sql
DROP [TEMPORARY] TABLE [IF EXISTS]
    tbl_name [, tbl_name] ...
    [RESTRICT | CASCADE]
```

删除一个或多个表

- 对于删除的每个表，都会删除表定义和所有表数据。如果表已分区，则该语句将删除表定义及其所有分区，存储在这些分区中的所有数据以及与删除的表关联的所有分区定义也将被删除。删除表还会删除该表的所有触发器
- 删除表时，不会自动删除对该表的授予。必须手动删除它们
- TEMPORARY 表示仅删除`TEMPORARY`表，防止意外删除非`TEMPORARY` 表





### 清空

TRUNCATE TABLE

语法格式

```sql
TRUNCATE [TABLE] tbl_name
```

详细解释参考[官方文档](https://dev.mysql.com/doc/refman/8.0/en/truncate-table.html)



### 重命名

RENAME TABLE 

语法格式

```sql
RENAME TABLE
    tbl_name TO new_tbl_name
    [, tbl_name2 TO new_tbl_name2] ...
```

重命名一个或多个表。必须具有 ALTER 与  DROP 原始表的权限，以及 CREATE 与  INSERT 新表的权限

```sql
RENAME TABLE old_table TO new_table;
-- 等同于
ALTER TABLE old_table RENAME new_table;
```

-  RENAME TABLE 与相比 ALTER TABLE )，可以在单个语句中重命名多个表

- 重命名操作从左到右执行。因此，要交换两个表名，请执行以下操作（假设具有中间名的表`tmp_table`尚不存在）：

  ```sql
  RENAME TABLE old_table TO tmp_table,
               new_table TO old_table,
               tmp_table TO new_table;
  ```

- 表上的元数据锁是按名称顺序获取的，在某些情况下，当多个事务同时执行时，操作结果可能会有所不同

- 从MySQL 8.0.13开始，您可以重命名使用 `LOCK TABLES` 语句锁定的表，前提是它们已被`WRITE`锁锁定，或者是`WRITE`从多表重命名操作中的较早步骤重命名已锁定表的产品

- 在MySQL 8.0.13之前，要执行`RENAME TABLE`，必须没有用锁定的表`LOCK TABLES`





## INDEX：索引

### 创建

CREATE INDEX

语法格式

```sql
CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX index_name
    [index_type]
    ON tbl_name (key_part,...)
    [index_option]
    [algorithm_option | lock_option] ...
```

各种属性的定义和取值，详细请参考[官方文档](https://dev.mysql.com/doc/refman/8.0/en/create-index.html)

```sql

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_option: {
    KEY_BLOCK_SIZE [=] value
  | index_type
  | WITH PARSER parser_name
  | COMMENT 'string'
  | {VISIBLE | INVISIBLE}
  | ENGINE_ATTRIBUTE [=] 'string'
  | SECONDARY_ENGINE_ATTRIBUTE [=] 'string'
}

index_type:
    USING {BTREE | HASH}

algorithm_option:
    ALGORITHM [=] {DEFAULT | INPLACE | COPY}

lock_option:
    LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
```





### 删除

DROP INDEX

语法格式

```
DROP INDEX index_name ON tbl_name
    [algorithm_option | lock_option] ...
```

相关选项

```
algorithm_option:
    ALGORITHM [=] {DEFAULT | INPLACE | COPY}

lock_option:
    LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
```

删除索引

- 要删除主键，索引名称始终为 `PRIMARY`，必须将其指定为带引号的标识符，因为`PRIMARY`它是保留字。

  ```sql
  DROP INDEX `PRIMARY` ON table_name;
  ```

  





## VIEW：视图

### 创建

CREATE VIEW

语法格式

```
CREATE
    [OR REPLACE]
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = user]
    [SQL SECURITY { DEFINER | INVOKER }]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

[文档](https://dev.mysql.com/doc/refman/8.0/en/create-view.html)



### 修改

ALTER VIEW

语法格式

```sql
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = user]
    [SQL SECURITY { DEFINER | INVOKER }]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

[文档](https://dev.mysql.com/doc/refman/8.0/en/alter-view.html)





### 删除

DROP VIEW

语法格式

```sql
DROP VIEW [IF EXISTS]
    view_name [, view_name] ...
    [RESTRICT | CASCADE]
```

[文档](https://dev.mysql.com/doc/refman/8.0/en/drop-view.html)





## 更多

触发器、函数、事件等等更多的操作，请查阅[官方文档](https://dev.mysql.com/doc/refman/8.0/en/sql-data-definition-statements.html)，后期有时间再进行详细了解