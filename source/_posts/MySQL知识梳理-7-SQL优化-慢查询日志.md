---
title: MySQL知识梳理-7.SQL优化-慢查询日志
abbrlink: a27033e6
date: 2021-03-26 21:23:47
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218912854.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218912854.jpg'
---





# 开启慢查询日志

### 查看当前的配置

```mysql
show variables like 'slow_query%';
show variables like 'long_query_time';
show variables like 'log_queries_not_using_indexes';
```



### 配置文件开启（永久生效）

```
slow_query_log = 1
slow_query_log_file=/usr/local/mysql8.0/sql_log/slowlog.log
long_query_time = 2
log_queries_not_using_indexes = 1
```

- slowquerylog = 1      表示开启慢查询
- slowquerylogfile      表示慢查询日志存放的位置，`low_query_log_file 的路径不能随便写，否则 MySQL 服务器可能没有权限将日志文件写到指定的目录中`
- longquerytime = 2      表示查询 >=2 秒才记录日志 最小支持0.001 即100毫秒，也可以设置为0，所有的sql都会记录
- logqueriesnotusing_indexes = 1    记录没有使用索引的 SQL 语句。

修改保存文件后，需要重启 MySQL 服务配置才能生效



### 设置MySQL全局配置变量(临时生效)

```mysql
set global slow_query_log=1;
set global slow_query_log_file='/usr/local/mysql8.0/sql_log/slowlog.log';
set global long_query_time=2;
set global log_queries_not_using_indexes = 1;
```

设置之后立即生效，但重启mysql服务之后就会失效



# 慢日志分析

## mysqldumpslow 命令

MySQL自带的分析命令。`使用mysqldumpslow的分析结果不会显示具体完整的sql语句,只会显示sql的组成结构`

```
mysqldumpslow --help

# 使用方法
Usage: mysqldumpslow [ OPTS... ] [ LOGS... ]

Parse and summarize the MySQL slow query log. Options are

  --verbose    verbose
  --debug      debug
  --help       write this text to standard output

  -v           verbose
  -d           debug
  -s ORDER     what to sort by (al, at, ar, c, l, r, t), 'at' is default
                al: average lock time
                ar: average rows sent
                at: average query time
                 c: count
                 l: lock time
                 r: rows sent
                 t: query time  
  -r           reverse the sort order (largest last instead of first)
  -t NUM       just show the top n queries
  -a           don't abstract all numbers to N and strings to 'S'
  -n NUM       abstract numbers with at least n digits within names
  -g PATTERN   grep: only consider stmts that include this string
  -h HOSTNAME  hostname of db server for *-slow.log filename (can be wildcard),
               default is '*', i.e. match all
  -i NAME      name of server instance (if using mysql.server startup script)
  -l           don't subtract lock time from total time
```

常用参数

- -s     sort，按照那种方式排序   
  - c：访问计数
  - l：锁定时间
  - r：返回记录
  - t：查询时间
  - al：平均锁定时间    
  - ar：平均访问记录数    
  - at：平均查询时间
-  -t     是top n的意思，返回多少条数据。
-  -g     可以跟上正则匹配模式，大小写不敏感



常见用法

```bash
mysqldumpslow -s c -t 10 /usr/local/mysql8.0/sql_log/slowlog.log # 取出使用最多的10条慢查询

mysqldumpslow -s t -t 3 /usr/local/mysql8.0/sql_log/slowlog.log # 取出查询时间最慢的3条慢查询

mysqldumpslow -s t -t 10 -g “left join” /usr/local/mysql8.0/sql_log/slowlog.log # 得到按照时间排序的前10条里面含有左连接的查询语句

mysqldumpslow -s r -t 10 -g 'left join' /usr/local/mysql8.0/sql_log/slowlog.log # 按照扫描行数最多的
```





## pt-query-digest

pt-query-digest是用于分析mysql慢查询的一个工具，它可以分析binlog、General log、slowlog，也可以通过``SHOW PROCESSLIST`或者通过 `tcpdump` 抓取的MySQL协议数据来进行分析。可以把分析结果输出到文件中，分析过程是先对查询语句的条件进行参数化，然后对参数化以后的查询进行分组统计，统计出各查询的执行时间、次数、占比等，可以借助分析结果找出问题进行优化。

pt-query-digest是一个perl脚本，只需下载并赋权即可执行。



### 安装

```
-- rpm包安装  这是一个工具集
wget https://downloads.percona.com/downloads/percona-toolkit/3.3.0/binary/redhat/7/x86_64/percona-toolkit-3.3.0-1.el7.x86_64.rpm
yum -y  install perl-Time-HiRes
rpm -ivh percona-toolkit-3.3.0-1.el7.x86_64.rpm --force --nodeps
```

### 使用

语法

`pt-query-digest --help` 查看命令帮助，以下只列出常用的参数

```
Usage: pt-query-digest [OPTIONS] [FILES] [DSN]

--create-review-table  	当使用--review参数把分析结果输出到表中时，如果没有表就自动创建。
--create-history-table  当使用--history参数把分析结果输出到表中时，如果没有表就自动创建。
--filter  	对输入的慢查询按指定的字符串进行匹配过滤后再进行分析
--limit		限制输出结果百分比或数量，默认值是20,即将最慢的20条语句输出，如果是50%则按总响应时间占比从大到小排序，输出到总和达到50%位置截止。
--host  	mysql服务器地址
--user  	mysql用户名
--password  mysql用户密码
--history 	将分析结果保存到表中，分析结果比较详细，下次再使用--history时，如果存在相同的语句，且查询所在的时间区间和历史表中的不同，则会记录到数据表中，可以通过查询同一CHECKSUM来比较某类型查询的历史变化。
--review 	将分析结果保存到表中，这个分析只是对查询条件进行参数化，一个类型的查询一条记录，比较简单。当下次使用--review时，如果存在相同的语句分析，就不会记录到数据表中。
--output 	分析结果输出类型，值可以是report(标准分析报告)、slowlog(Mysql slow log)、json、json-anon，一般使用report，以便于阅读。
--since 	从什么时间开始分析，值为字符串，可以是指定的某个”yyyy-mm-dd [hh:mm:ss]”格式的时间点，也可以是简单的一个时间值：s(秒)、h(小时)、m(分钟)、d(天)，如12h就表示从12小时前开始统计。
--until 	截止时间，配合—since可以分析一段时间内的慢查询。
```

用法示例

```
# 直接分析慢查询日志 并将分析结果输出到slow_report.log
pt-query-digest  slow.log > slow_report.log

# 分析最近12个小时的慢查询 并将分析结果输出到slow_report2.log
pt-query-digest  --since=12h  slow.log > slow_report2.log

# 分析指定时间范围内的查询
pt-query-digest slow.log --since '2014-05-17 09:30:00' --until '2014-06-17 10:00:00'> > slow_report3.log

# 分析只含有select语句的慢查询
pt-query-digest --filter '$event->{fingerprint} =~ m/^select/i' slow.log> slow_report4.log

# 针对某个用户的慢查询
pt-query-digest --filter '($event->{user} || "") =~ m/^root/i' slow.log> slow_report5.log

# 查询所有的全表扫描或full join的慢查询
pt-query-digest --filter '(($event->{Full_scan} || "") eq "yes") ||(($event->{Full_join} || "") eq "yes")' slow.log> slow_report6.log

# 把查询保存到test数据库的query_review表,如果没有的话会自动创建;
pt-query-digest  --user=root –password=abc123 --review  h=localhost,D=test,t=query_review --create-review-table  slow.log

# 把查询保存到query_history表
pt-query-digest  --user=root –password=abc123 --review  h=localhost,D=test,t=query_ history --create-review-table  slow.log_20140401

# 通过tcpdump抓取mysql的tcp协议数据，然后再分析
tcpdump -s 65535 -x -nn -q -tttt -i any -c 1000 port 3306 > mysql.tcp.txt
pt-query-digest --type tcpdump mysql.tcp.txt> slow_report9.log

# 分析binlog
mysqlbinlog mysql-bin.000093 > mysql-bin000093.sql
pt-query-digest  --type=binlog  mysql-bin000093.sql > slow_report10.log

# 分析general log
pt-query-digest  --type=genlog  localhost.log > slow_report11.log
```





### 分析结果说明

第一部分：总体统计结果

![](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20210327135246.png)

- Overall:   总共有多少条查询，上例为总共266个查询。unique: 唯一查询数量，即对查询条件进行参数化以后，总共有多少个不同的查询，该例为4。

- Time range:   查询执行的时间范围

- total: 总计 

- min:最小 

- max: 最大

-  avg:平均

- 95%: 把所有值从小到大排列，位置位于95%的那个数，这个数一般最具有参考价值。
- median: 中位数，把所有值从小到大排列，位置位于中间那个数



第二部分: 查询分组统计结果

![](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20210327135641.png)

这部分对查询进行参数化并分组，然后对各类查询的执行情况进行分析，结果按总执行时长，从大到小排序

- Response: 总的响应时间。
- time: 该查询在本次分析中总的时间占比。
- calls: 执行次数，即本次分析总共有多少条这种类型的查询语句。
- R/Call: 平均每次执行的响应时间。
- V/M : 查询对象



第三部分：每一种查询的详细统计结果

![](https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/img/20210327140053.png)



由上图可见,1号查询的详细统计结果，最上面的表格列出了执行次数、最大、最小、平均、95%等各项目的统计。
Databases:    库名
Users:     各个用户执行的次数（占比）
Query_time distribution :     查询时间分布, 长短体现区间占比，本例中1s-10s之间查询数量没有,全部集中在10S里面。
Tables:     查询中涉及到的表



`可以结合 Anemometer 开源项目实现慢查询日志分析可视化`



## 实时获取慢sql

查询 information_schema.PROCESSLIST 表 的TIME大于一定时间的

```mysql
SELECT * FROM  information_schema.PROCESSLIST WHERE `TIME` > 1;
```







































