---
title: MySQL知识梳理-10.锁
abbrlink: 9333edc7
date: 2021-04-07 10:54:16
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218831023.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218831023.jpg'
---





# MySQL锁的介绍

**什么是锁**

在数据库中，除传统计算资源（CPU、RAM、I\O等）的争抢，数据也是一种供多用户共享的资源。
如何保证数据并发访问的一致性，有效性，是所有数据库必须要解决的问题。
锁冲突也是影响数据库并发访问性能的一个重要因素，因此锁对数据库尤其重要

**锁的作用**

计算机协调多个进程或线程并发访问某一资源的机制。解决资源竞争的问题，使得资源可以独享。

**缺点**

加锁是消耗资源的，锁的各种操作，包括获得锁、检测锁是否已解除、释放锁等 ，都会增加系统的开销。



# 锁的分类

**按锁的使用方式**

- 悲观锁
- 乐观锁

**按锁的锁定粒度**

- 全局锁
- 表锁

- 行锁

- 页锁



# 各种锁的含义

这里部分的锁只做了简单介绍，后期如有需要会专门写文章进行详解



## 悲观锁

认为本次操作会发生并发冲突，所以在进行每次操作时都要通过获取锁才能进行对相同数据的操作，悲观锁需要耗费较多的时间。数据库实现，要用的时候，我们直接调用数据库的相关语句就可以了，即每次操作数据前手动调用SQL加锁



## 乐观锁

认为本次操作不会发生并发冲突，在操作数据时并不进行任何特殊处理（不加锁），只在提交操作时检查是否违反数据完整性。业务代码实现

通常实现是这样的：

在表中的数据进行操作时(更新)，先给数据表加一个版本(version)字段，每操作一次，将那条记录的版本号加1。也就是先查询出那条记录，获取出version字段,如果要对那条记录进行操作(更新),则先判断此刻version的值是否与刚刚查询出来时的version的值相等，如果相等，则说明这段期间，没有其他程序对其进行操作，则可以执行更新，将version字段的值加1；如果更新时发现此刻的version值与刚刚获取出来的version的值不相等，则说明这段期间已经有其他程序对其进行操作了，则不进行更新操作。



## 读锁（Read Lock） / 共享锁（Share Lock）

针对同一份数据，多个读操作可以同时进行而不会相互影响



## 写锁（Write Lock） / 排他锁（Exclusive Lock）/ 独占锁

当前写操作没完成之前，会阻塞其他读和写操作



## 全局锁

全局锁就是对`整个数据库实例`加锁。注意锁定的是实例而不是某个数据库

### 加锁 & 释放锁

 ```mysql
-- 加锁
Flush tables with read lock;
-- 释放锁
UNLOCK TABLES;
-- 会话关闭后锁也会主动被释放
 ```

**加锁方式简称 FTWRL，当你需要让整个库处于只读状态的时候，使用这个命令，之后其他线程的以下语句会被阻塞：数据更新语句（数据的增删改）、数据定义语句（包括建表、修改表结构等）和更新类事务的提交语句。**

执行FTWRL后的详细说明：

- 所有会话都可以读，包括当前会话、当前实例的其他会话
- 当前会话的数据更新语句（数据的增删改）、数据定义语句（包括建表、修改表结构等）和更新类事务的提交语句会报错 `1223 - Can't execute the query because you have a conflicting read lock`
- 其他会话的数据更新语句（数据的增删改）、数据定义语句（包括建表、修改表结构等）和更新类事务的提交语句会被阻塞



**另一种方式设置全局参数也可以达到一样的效果**

```mysql
set global readonly=true;
```

**相比之下建议使用FTWRL的方法，原因如下：**

- 有些系统中，readonly 的值会被用来做其他逻辑，比如用来判断一个库是主库还是备库。因此，修改 global 变量的方式影响面更大
- 在异常处理机制上有差异。如果执行`FTWRL 命令之后由于客户端发生异常断开，那么 MySQL 会自动释放这个全局锁`，整个库回到可以正常更新的状态。而将整个库设置为 readonly 之后，如果客户端发生异常，则数据库就会一直保持 readonly 状态，这样会导致整个库长时间处于不可写状态，风险较高
- readonly 对super用户权限无效



`注意：业务的更新不只是增删改数据（DML)，还有可能是加字段等修改表结构的操作（DDL）。不论是哪种方法，一个库被全局锁上以后，你要对里面任何一个表做加字段操作，都是会被锁住的。`



### 使用场景

- 全库逻辑备份（mysqldump）

- 重新做主从时候
- 数据同步





## 元数据锁（MDL Lock）

metadata lock，表级锁。MDL锁主要作用是维护`表元数据`的数据一致性，在表上有活动事务（显式或隐式）的时候，不可以对元数据进行写入操作。从MySQL5.5版本开始引入了MDL锁，来保护表的元数据信息，用于解决或者保证DDL操作与DML操作之间的一致性

**主要解决的问题**

1. 事务隔离问题，比如在可重复隔离级别下，会话A在2次查询期间，会话B对表结构做了修改，两次查询结果就会不一致，无法满足可重复读的要求
2. 数据复制的问题，比如会话A执行了多条更新语句期间，另外一个会话B做了表结构变更并且先提交，就会导致slave在重做时，先重做alter，再重做update时就会出现复制错误的现象

**说明**

`元数据锁是server层的锁，MDL加锁过程是系统自动控制，无法直接干预`，每执行一条DML、DDL语句时都会申请MDL锁

DML操作需要MDL读锁，DDL操作需要MDL写锁（读读共享，读写互斥，写写互斥）

申请MDL锁的操作会形成一个队列，队列中写锁获取优先级高于读锁。一旦出现写锁等待，不但当前操作会被阻塞，同时还会阻塞后续该表的所有操作。

事务一旦申请到MDL锁后，直到事务执行完才会将锁释放。（这里有种特殊情况如果事务中包含DDL操作，mysql会在DDL操作语句执行前，隐式提交commit，以保证该DDL语句操作作为一个单独的事务存在，同时也保证元数据排他锁的释放）

`注意：支持事务的InnoDB引擎表和不支持事务的MyISAM引擎表，都会出现Metadata Lock Wait等待现象。一旦出现Metadata Lock Wait等待现象，后续所有对该表的访问都会阻塞在该等待上，导致连接堆积，业务受影响`



MySQL5.7中，performance_schema库中新增了metadata_locks表，专门记录MDL的相关信息。首先要开启MDL锁记录，执行如下SQL开启

```mysql
UPDATE performance_schema.setup_instruments SET ENABLED = 'YES', TIMED = 'YES' WHERE NAME = 'wait/lock/metadata/sql/mdl';
```

测试

会话1 先执行

```mysql
BEGIN;
SELECT * FROM tb_brand;

INSERT INTO `shenquan`.`tb_brand`(`id`, `name`, `sort`, `last_admin_id`, `status`, `create_time`, `update_date_time`) VALUES (null, '星巴克', 88888, 1, 1, 1602513341, '2020-10-12 22:35:41');
```

会话2 再执行

```mysql
ALTER TABLE tb_brand ADD test INT AFTER update_date_time;
```

然后会话3查看进程列表就会发现出现元数据锁

```mysql
show processlist;
```

![image-20210417142729837](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/image-20210417142729837.png)

查询元数据锁的记录

```mysql
SELECT * FROM performance_schema.metadata_locks;
```

![image-20210417143105290](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/image-20210417143105290.png)

还可以结合其他系统表查出元数据锁的会话ID

```mysql
select m.*,t.PROCESSLIST_ID from performance_schema.metadata_locks m left join performance_schema.threads t on m.owner_thread_id=t.thread_id;
```

![image-20210417143248563](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/image-20210417143248563.png)

这时候就可以根据实际情况去处理元数据锁了 ，比如根据进程ID KILL掉相应的进程



**如何优化与避免MDL锁**

- 开启metadata_locks表记录MDL锁。
- 设置参数lock_wait_timeout为较小值，使被阻塞端主动停止。
- 规范使用事务，及时提交事务，避免使用大事务。
- 增强监控告警，及时发现MDL锁。
- DDL操作及备份操作放在业务低峰期执行。
- 少用工具开启事务进行查询，图形化工具要及时关闭





## 表锁

表级锁。MyIsam和InnoDB引擎都支持，MyIsam只支持表锁不支持行锁，也是MyIsam的默认锁

### 表共享锁（读锁）

当前会话接下来只允许对锁定的表进行读操作，其他会话对锁定表的写操作会被阻塞（其他不影响）

|          | 锁定表（读） | 锁定表（写）                                                 | 其他表（读）                                                 | 其他表（写）                                                 |
| -------- | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 当前会话 | 正常执行     | 报错 `ERROR 1099 (HY000): Table 'tb_brand' was locked with a READ lock and can't be updated` | 报错 `ERROR 1100 (HY000): Table 'tb_category' was not locked with LOCK TABLES` | 报错 `ERROR 1100 (HY000): Table 'tb_category' was not locked with LOCK TABLES` |
| 其他会话 | 正常执行     | 阻塞                                                         | 正常执行                                                     | 正常执行                                                     |

```mysql
/**
	会话1
**/
-- 锁定 tb_brand
Lock Table tb_brand READ;
-- 对锁定表读操作 正常执行
SELECT * FROM tb_brand;
-- 对锁定表写操作 报错 Table 'tb_brand' was locked with a READ lock and can't be updated
UPDATE tb_brand SET `name` = '肯德基' WHERE id = 14;
-- 对其他表的读写操作 报错 Table 'tb_category' was not locked with LOCK TABLES
SELECT * FROM tb_category;
UPDATE tb_category SET `name` = '肯德基' WHERE id = 14;
-- 释放锁
UNLOCK TABLES;

/**
	会话2
**/
-- 对锁定表读操作  正常执行
SELECT * FROM tb_brand;
-- 对锁定表写操作  会被阻塞
UPDATE tb_brand SET `name` = '肯德基' WHERE id = 14;
-- 对其他表读写操作  正常执行
SELECT * FROM tb_category;
UPDATE tb_category SET `name` = '肯德基' WHERE id = 14;

```



### 表排他锁（写锁、独占锁）

当前会话只允许对锁定表进行读写，其他会话对锁定表的读写操作会被阻塞

|          | 锁定表（读写） | 其他表（读写）                                               |
| -------- | -------------- | ------------------------------------------------------------ |
| 当前会话 | 正常执行       | 报错 `ERROR 1100 (HY000): Table 'tb_category' was not locked with LOCK TABLES` |
| 其他会话 | 阻塞           | 正常执行                                                     |

```mysql
/**
	会话1
**/
-- 锁定 tb_brand
Lock Table tb_brand WRITE;
-- 对锁定表读写操作 正常执行
SELECT * FROM tb_brand;
UPDATE tb_brand SET `name` = '肯德基' WHERE id = 14;
-- 对其他表的读写操作 报错 ERROR 1100 (HY000): Table 'tb_category' was not locked with LOCK TABLES
SELECT * FROM tb_category;
UPDATE tb_category SET `name` = '肯德基' WHERE id = 14;
-- 释放锁
UNLOCK TABLES;

/**
	会话2
**/
-- 对锁定表读写操作  会被阻塞
SELECT * FROM tb_brand;
UPDATE tb_brand SET `name` = '肯德基' WHERE id = 14;
-- 对其他表读写操作  正常执行
SELECT * FROM tb_category;
UPDATE tb_category SET `name` = '肯德基' WHERE id = 14;
```



### 加锁与释放锁

**隐式**

```mysql
-- 共享锁
SELECT
-- 排他锁
INSERT UPDATE DELETE
-- 释放锁  不需要  自动释放
```

**显式**

```mysql
-- 共享锁
Lock Table table_name READ;
-- 排他锁
Lock Table table_name WRITE;
-- 释放锁  以下命令或者会话关闭
UNLOCK TABLES;
```



### 查看表锁情况

```mysql
show open tables;
```



### 分析锁

```mysql
SHOW status LIKE 'table%';

```

- table_locks_waited  出现表级锁定争用而发生等待的次数（不能立即获取锁的次数，每等待一次值加1），此值高说明存在着较严重的表级锁争用情况
- table_locks_immediate  产生表级锁定次数，不是可以立即获取锁的查询次数，每立即获取锁+1



## 行锁（Record Lock）

InnoDB 支持行锁，默认锁。行锁是通过索引上的索引项来实现的，也就是说`只有通过索引条件检索数据，InnoDB才会使用行级锁，否则，InnoDB将使用表锁！`

### 行共享锁

允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。

|          | 锁定行（读） | 锁定行（写） | 其他行（读） | 其他行（读） |
| -------- | ------------ | ------------ | ------------ | ------------ |
| 当前会话 | 正常执行     | 阻塞         | 正常执行     | 正常执行     |
| 其他会话 | 正常执行     | 阻塞         | 正常执行     | 正常执行     |

```mysql
/**
	会话1
**/
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 LOCK IN SHARE MODE;
UPDATE tb_brand_1 SET name = '' WHERE id = 14;
SELECT * FROM tb_brand_1 WHERE id = 15;
UPDATE tb_brand_1 SET name = '' WHERE id = 15;
ROLLBACK;
/**
	会话2
**/
SELECT * FROM tb_brand_1 WHERE id = 14;
UPDATE tb_brand_1 SET name = '' WHERE id = 14;
SELECT * FROM tb_brand_1 WHERE id = 15;
UPDATE tb_brand_1 SET name = '' WHERE id = 15;
```



**隐式加锁**

```
SELECT 默认加 S 锁
```

**显式加锁**

```mysql
SELECT * FROM table_name WHERE `index` = 1 LOCK IN SHARE MODE;
```

**释放锁**

事务提交或者回滚、会话关闭



### 行排他锁

允许获取排他锁的事务更新数据，阻止其他事务取得相同的数据集共享读锁和排他写锁

|          | 锁定行（读） | 锁定行（写） | 其他行（读） | 其他行（读） |
| -------- | ------------ | ------------ | ------------ | ------------ |
| 当前会话 | 正常执行     | 正常执行     | 正常执行     | 正常执行     |
| 其他会话 | 正常执行     | 阻塞         | 正常执行     | 正常执行     |

```mysql
-- 实验1
-- 全部会话1执行
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
SELECT * FROM tb_brand_1 WHERE id = 14;
ROLLBACK;

-- 实验2
-- 全部会话1执行
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
UPDATE tb_brand_1 SET name = '' WHERE id = 14;
ROLLBACK;

-- 实验3
-- 全部会话1执行
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
SELECT * FROM tb_brand_1 WHERE id = 15;
ROLLBACK;

-- 实验4
-- 全部会话1执行
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
UPDATE tb_brand_1 SET name = '' WHERE id = 15;
ROLLBACK;



-- 实验5
-- 会话1
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
-- 会话2
SELECT * FROM tb_brand_1 WHERE id = 14;
-- 会话1
ROLLBACK;

-- 实验6
-- 会话1
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
-- 会话2
UPDATE tb_brand_1 SET name = '' WHERE id = 14;
-- 会话1
ROLLBACK;

-- 实验7
-- 会话1
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
-- 会话2
SELECT * FROM tb_brand_1 WHERE id = 15;
-- 会话1
ROLLBACK;

-- 实验8
-- 会话1
BEGIN;
SELECT * FROM tb_brand_1 WHERE id = 14 FOR UPDATE;
-- 会话2
UPDATE tb_brand_1 SET name = '' WHERE id = 15;
-- 会话1
ROLLBACK;
```



**隐式加锁**

```
DELETE / UPDATE / INSERT 默认加上X锁
```

**显式加锁**

```mysql
SELECT * FROM student WHERE id = 1 FOR UPDATE;
```

**释放锁**

事务提交或者回滚、会话关闭



**InnoDB行锁模式兼容性列表**

| 当前锁模式/请求锁模式 | X    | IX   | S    | IS   |
| --------------------- | ---- | ---- | ---- | ---- |
| X                     | 冲突 | 冲突 | 冲突 | 冲突 |
| IX                    | 冲突 | 兼容 | 冲突 | 兼容 |
| S                     | 冲突 | 冲突 | 兼容 | 兼容 |
| IS                    | 冲突 | 兼容 | 兼容 | 兼容 |



### 行锁分析

```mysql
SHOW STATUS LIKE 'innodb_row_lock%';
```

- innodb_row_lock_current_waits     当前正在等待锁定的数量
- innodb_row_lock_time                     从系统启动到现在锁定总时间长度
- innodb_row_lock_time_avg             每次等待所花平均时间
- innodb_row_lock_time_max            从系统启动到现在等待最长的一次所花时间
- innodb_row_lock_waits                    系统启动后到现在总共等待的次数

#### information_schema 库

1. innodb_lock_waits表
2. innodb_locks表
3. innodb_trx表



### 优化建议

1. 尽可能让所有数据检索都通过索引来完成，避免无索引行锁升级为表锁
2. 合理设计索引，尽量缩小锁的范围
3. 尽可能较少检索条件，避免间隙锁
4. 尽量控制事务大小，减少锁定资源量和时间长度
5. 尽可能低级别事务隔离



## 间隙锁

当使用范围条件而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给符合条件的已有数据记录的索引加锁，对于键值在条件范围内但并不存在的记录。间隙锁在InnoDB的唯一作用就是防止其它事务的插入操作，以此来达到防止幻读的发生，所以间隙锁不分什么共享锁与排它锁

注意：`其实锁定的是这个范围不存在的记录的索引间隙，但不包括已经存在的记录`

比如记录如下：

```
id
14	肯德基	88888	1	1	1597388251	2021-04-17 11:25:00
16	爱奇艺	88888	1	1	1597388267	2020-08-14 14:57:47
18	中国电信	88888	1	1	1597388295	2020-08-14 14:58:15
21	星巴克	88888	1	1	1602513341	2020-10-12 22:35:41
22	星巴克	88888	1	1	1602513341	2020-10-12 22:35:41
```

这时候使用

```mysql
SELECT * FROM table_name WHERE id < 14 FOR UPDATE;
```

锁定是的  负无穷~13 

此时如果其他会话想插入id=10的记录就没办法插入，因为已经被上面那个会话锁定了，但是修改id=14的还是可以修改的



查看是否开启间隙锁

```mysql
show variables like 'innodb_locks_unsafe_for_binlog';
```



关闭间隙锁(gap lock)方法:

在my.cnf里面的[mysqld]添加

```
[mysqld]
innodb_locks_unsafe_for_binlog = 1
```





## 临键锁

行锁+ 间隙锁就是临键锁

比如记录如下：

```
id
14	肯德基	88888	1	1	1597388251	2021-04-17 11:25:00
16	爱奇艺	88888	1	1	1597388267	2020-08-14 14:57:47
18	中国电信	88888	1	1	1597388295	2020-08-14 14:58:15
21	星巴克	88888	1	1	1602513341	2020-10-12 22:35:41
22	星巴克	88888	1	1	1602513341	2020-10-12 22:35:41
```

这时候使用

```mysql
SELECT * FROM table_name WHERE id < 20 FOR UPDATE;
```

锁定是的  负无穷~19在内的所有记录

其他会话既不能插入id=10的记录也没办法修改id=14的记录



## 意向锁

意向锁是由数据引擎自己维护的，用户无法手动操作意向锁。是为了允许行锁和表锁共存，实现多粒度锁机制

- 意向共享锁（Intention Shared Lock）
  - 简称 IS 锁，表示事务准备给数据行加入共享锁，也就是说一个数据行加共享锁前必须先取得该表的 IS 锁
- 意向排他锁（Intention Exclusive Lock）
  - 简称 IX 锁，表示事务准备给数据行加入排他锁，说明事务在一个数据行加排他锁前必须先取得该表的 IX 锁





## 页锁







## 死锁

两个事务都持有对方需要的锁，并且在等待对方释放，并且双方都不会释放自己的锁。

### 产生原因

- 互斥条件：一个资源每次只能被一个进程使用
- 请求与保持：一个进程因请求资源而阻塞时，对已获得的资源保持不放
- 不剥夺：进程已获取的资源，在没有使用完之前，不能强行剥夺
- 循环等待：多个进程之间形成的一种互相循环等待资源的关系

### 解决死锁

**查看死锁**

```
SHOW ENGINE innodb status;
```

MySQL有自动检测死锁的机制，超时会自动回滚代价较大的事务已保证其他事务正常执行。`innodb_lock_wait_timeout` 默认是50s，设置值小一点可有利于更快检测到。

人为检测：使用 `SHOW PROCESSLIST;` 查看进程，发现死锁使用KILL 进程id来杀掉死锁进程





**如何减少死锁：**

1. 使用事务，不使用 lock tables 。
2. 保证没有长事务。
3. 操作完之后立即提交事务，特别是在交互式命令行中。
4. 如果在用 (SELECT ... FOR UPDATE or SELECT ... LOCK IN SHARE MODE)，尝试降低隔离级别。
5. 修改多个表或者多个行的时候，将修改的顺序保持一致。
6. 创建索引，可以使创建的锁更少。
7. 最好不要用 (SELECT ... FOR UPDATE or SELECT ... LOCK IN SHARE MODE)。
8. 如果上述都无法解决问题，那么尝试使用 lock tables t1, t2, t3 锁多张表





# 常用数据库引擎支持的锁

**MyISAM支持的锁**

表锁（默认锁）

**InnoDB支持的锁**

表锁和行锁（默认锁）







# 表锁 VS 行锁

## 对比

锁定粒度：表锁 > 行锁

加锁效率：表锁 > 行锁

冲突概率：表锁 > 行锁

并发性能：表锁 < 行锁















