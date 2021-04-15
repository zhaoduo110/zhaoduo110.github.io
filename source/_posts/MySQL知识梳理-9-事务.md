---
title: MySQL知识梳理-9.事务
abbrlink: 1704ede5
date: 2021-04-02 15:14:17
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218938852.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218938852.jpg'
---





# 什么是事务

>   个人理解：事务是一组SQL的逻辑执行单元，可以是一条SQL也可以是多条SQL，事务保证这组SQL要么全部执行成功要么全部放弃



# 使用事务

MySQL中InnoDB引擎支持事务



### 隐式事务处理（个人这么叫的）

执行一条SQL其实也是一个事务，MySQL中的事务是默认提交的，即非显示开启事务的情况下，执行SQL时MYSQL引擎会自动去提交事务

**设置**

`SHOW VARIABLES LIKE 'autocommit';`  查看当前自动提交事务是否开启，

`set autocommit=0;`  关闭`当前会话`的事务自动提交，这时再执行SQL就不会主动提交了，需要执行 `commit` 进行提交或者 `rollback` 回滚事务

配置文件 mysqld 配置中 添加**autocommit=0** 可永久关闭自动提交

### 显示事务处理

```sql
BEGIN; / START TRANSACTION;
	SQL1
	SQL2
	...
COMMIT; / ROLLBACK;
```

不管事务是否是自动提交的

在显示使用 `BEGIN;` 或者 `START TRANSACTION;` 开启事务时，都必须使用 `COMMIT;` 进行事务提交 或者 `ROLLBACK;`来回滚事务



# 事务的特性

## A    原子性（Atomicity）

一个事务中的所有操作，要么全部完成要么全部不完成，不会结束在中间的某个环节

>具体的实现是基于 MySQL的undo log来做的，事务执行的每个操作都会记录到undo log中，如果触发了事务回滚，那么MySQL会根据undo log执行一个相反的操作进行反向补偿，比如如果是delete则进行一次相反的insert，如果是update则会进行一次相反的update

## C    一致性（Consistency）

在事务开始之前和事务结束以后，数据库的完整性没有被破坏

## I     隔离性（Isolation）

事务的隔离性要求每个读写事务的对象与其他事务的操作对象能相互分离，即该事务提交前对其他事务都不可见

**INNODB的隔离级别**

- 读未提交（READ UNCOMMITTED）
- 读已提交（READ COMMITTED）
- 可重复读（RERPEATABLE READ）
- 顺序读（SERIALIZABLE）

## D    持久性（Durability）

事务一旦提交了，其结果就是永久性的，就算发生了宕机等事故，数据库也能将数据恢复

>   MySQL是基于redo log来实现的，在向MySQL插入数据时，MySQL会首先会写到 redo log一条记录，由于redolog会向末尾追加的记录，所以写入效率非常高，然后再写入buffer，最后落盘，如果写入了redolog后服务器发生了宕机，MySQL再次启动时也会根据redolog完成接下来的工作，以此来保证了数据的持久性





# 并发情况下事务可能带来的问题

高并发的情况下事务可能会出现的问题。（忽略事务隔离级别的情况下会出现的问题）

**脏读**

事务A后面那次查询读取到了事务B未提交的数据(此时要修改的操作还在内存中)，导致的事务A中的两次读取结果不一样成为脏读

如果事物B回滚了事务，那么对于事务A来说就是读取到了脏数据，会导致数据最终的不一致

![image-20210402162057059](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/image-20210402162057059.png)

**不可重复读**

在事务A中后面的一次查询读取到了事务B已提交的`修改和删除`操作而导致的事务A中的两次读取结果不一样，称为不可重复读

![image-20210402162510388](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/image-20210402162510388.png)



**幻读**

事务A中后面那次查询读取到了事务B已提交的`插入的操作` 而导致事务A中的两次读取结果不一样称为幻读

![image-20210402162813454](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/image-20210402162813454.png)



**说明：**

1. 脏读与不可重复读、幻读的区别在于脏读是读取到未提交到事务的数据，而不可重复读和幻读都是读取到已提交的事务的数据

2. 不可重复读与幻读的区别在于 不可重复读读取到的是修改和删除的操作，幻读是插入



`事务并发带来的三个问题都是 数据读一致性问题，只能通过事务隔离机制来解决`



# 事务隔离级别

事务隔离级别主要是为了解决以上所述的脏读、不可重复读和幻读的事务问题

## MySQL四种事务隔离级别

| 事务隔离级别                     | 脏读   | 不可重复读 | 幻读           | 隔离性 | 性能 |
| -------------------------------- | ------ | ---------- | -------------- | ------ | ---- |
| Read Uncommitted（读未提交）     | 可能   | 可能       | 可能           | 最低   | 最高 |
| Read Committed （读已提交）      | 不可能 | 可能       | 可能           | \|     | ^    |
| Repeatable Read（可重复读）      | 不可能 | 不可能     | 对INNODB不可能 | V      | \|   |
| Serializable（顺序读（串行化）） | 不可能 | 不可能     | 不可能         | 最高   | 最低 |

顺序读隔离性最高，但是性能最低，生产环境不建议使用，为了保证事务一致性 读未提交也不建议使用 。`所以在生产环境 Innodb引擎建议使用Repeatable Read（可重复读）的隔离级别，这也是MySQL默认的事务隔离级别，因为这个隔离级别对INNODB引擎来讲不存在任何事务读取的问题，既保证了性能也解决了数据读一致性问题`



## 设置MySQL事务隔离级别

**查看当前的事务隔离级别**

集中写法暂时除了区分了是全局的还是会话级别的，暂时没找到其他区别

```mysql
SELECT @@global.tx_isolation;
SELECT @@global.transaction_isolation;

-- 以下方式都是设置会话级别的
SELECT @@session.tx_isolation;
SELECT @@session.transaction_isolation;

SELECT @@tx_isolation;
SELECT @@transaction_isolation;

SHOW VARIABLES LIKE 'tx_isolation';
SHOW VARIABLES LIKE 'transaction_isolation';
```

**设置**

基本语法

```
SET [SESSION | GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE}
```

-   使用GLOBAL关键字，语句在全局对从修改的时间点开始创建的所有新连接（除了不存在的连接）设置默认事务隔离级别。你需要SUPER权限来做这个。
-   使用SESSION 关键字为将来在当前连接上执行的事务设置默认事务隔离级别。 任何客户端都能自由改变会话隔离级别（甚至在事务的中间），或者为下一个事务设置隔离级别。

比如一下SQL是设置当前会话的事务隔离级别为读未提交

```
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```





## 实现事务隔离

如果要解决事务数据读一致性问题，保证一个事务前后两次读取数据结果一致，即自己实现事务隔离应该怎么做 ？

### 方案一

LBCC（Lock Based Concurrency Control）：基于锁的并发控制。在读取数据前对其加锁，阻止其他事务对数据进行修改

### 方案二

MVCC（Multi Version Concurrency Control）：多版本并发控制。生成一个数据请求时间点的一致性数据快照（Snapshot），并用这个快照来提供一定级别（语句级或事务级）的一致性读取。undo-log





















