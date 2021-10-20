---
title: MySQL知识梳理-3.MySQL配置详解及参数调优
categories:
  - - SQL
tags:
  - MySQL
  - SQL
top_img: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218831023.jpg'
cover: 'https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218831023.jpg'
abbrlink: cb78f86f
date: 2021-03-16 18:06:48
---





# MySQL配置文件详解

一般配置文件位于 /etc/my.cnf

## Demo

```
[client]
port            = 3306
socket          = /usr/local/mysql8.0/data/mysql.sock
[mysqld]
# Skip #
skip_name_resolve              = 1
skip_external_locking          = 1 
skip_symbolic_links     = 1
# GENERAL #
user = mysql
default_storage_engine = InnoDB
character-set-server = utf8
socket  = /usr/local/mysql8.0/data/mysql.sock
pid_file = /usr/local/mysql8.0/data/mysqld.pid
basedir = /usr/local/mysql8.0
port = 3306
bind-address = 0.0.0.0
explicit_defaults_for_timestamp = off
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
#read_only=on
# MyISAM #
key_buffer_size                = 32M
#myisam_recover                 = FORCE,BACKUP

# undo log #
innodb_undo_directory = /usr/local/mysql8.0/undo
innodb_undo_tablespaces = 8

# SAFETY #
max_allowed_packet             = 100M
max_connect_errors             = 1000000
sysdate_is_now                 = 1
#innodb = FORCE
#innodb_strict_mode = 1
secure-file-priv='/tmp'
default_authentication_plugin='mysql_native_password'
# Replice #
server-id = 1001
relay_log = mysqld-relay-bin
gtid_mode = on
enforce-gtid-consistency
log-slave-updates = on
master_info_repository =TABLE
relay_log_info_repository =TABLE

# DATA STORAGE #
datadir = /usr/local/mysql8.0/data/
tmpdir = /tmp

# BINARY LOGGING #
log_bin = /usr/local/mysql8.0/sql_log/mysql-bin
max_binlog_size = 1000M
binlog_format = row
binlog_expire_logs_seconds=86400
# sync_binlog = 1

# CACHES AND LIMITS #
tmp_table_size                 = 32M
max_heap_table_size            = 32M
max_connections                = 4000
thread_cache_size              = 2048
open_files_limit               = 65535
table_definition_cache         = 4096
table_open_cache               = 4096
sort_buffer_size               = 2M
read_buffer_size               = 2M
read_rnd_buffer_size           = 2M
# thread_concurrency             = 24
join_buffer_size = 1M
# table_cache = 32768
thread_stack = 512k
max_length_for_sort_data = 16k

# INNODB #
innodb_flush_method            = O_DIRECT
innodb_log_buffer_size = 16M
innodb_flush_log_at_trx_commit = 2
innodb_file_per_table          = 1
innodb_buffer_pool_size        = 256M
#innodb_buffer_pool_instances = 8
innodb_stats_on_metadata = off
innodb_open_files = 8192
innodb_read_io_threads = 16
innodb_write_io_threads = 16
innodb_io_capacity = 20000
innodb_thread_concurrency = 0
innodb_lock_wait_timeout = 60
innodb_old_blocks_time=1000
innodb_use_native_aio = 1
innodb_purge_threads=1
innodb_change_buffering=all
innodb_log_file_size = 64M
innodb_log_files_in_group = 2
innodb_data_file_path  = ibdata1:256M:autoextend

innodb_rollback_on_timeout=on
# LOGGING #
log_error                      = /usr/local/mysql8.0/sql_log/mysql-error.log
# log_queries_not_using_indexes  = 1
# slow_query_log                 = 1
slow_query_log_file            = /usr/local/mysql8.0/sql_log/slowlog.log

# TimeOut #
#interactive_timeout = 30
#wait_timeout        = 30
#net_read_timeout = 60

[mysqldump]
quick
max_allowed_packet = 100M

[mysql]
no-auto-rehash
# Remove the next comment character if you are not familiar with SQL
#safe-updates

[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 256M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout
```

## 配置详解

### 客户端设置

[client]

| 配置项 | 说明                                                         | 值       | 默认值          |
| ------ | ------------------------------------------------------------ | -------- | --------------- |
| port   | 端口                                                         | 端口号   | 3306            |
| socket | 默认情况下，socket文件应为`/usr/local/mysql/mysql.socket`,所以可以 `ln -s xx  /tmp/mysql.sock` | filename | /tmp/mysql.sock |

### 服务端设置

[mysqld]

| 配置项                | 说明                                                         | 值   | 默认值          |
| --------------------- | ------------------------------------------------------------ | ---- | --------------- |
| server-id             | 表示是本机的序号为1,一般来讲就是master的意思                 |      | 1               |
| port                  | 服务端口号 默认3306                                          |      | 3306            |
| user                  | 启动mysql服务进程的用户                                      |      |                 |
| basedir               | mysql安装根目录。/usr/local/mysql-5.7.21                     |      |                 |
| datadir               | mysql数据文件所在位置                                        |      |                 |
| tmpdir                | 临时目录 比如load data infile会用到,一般都是使用/tmp         |      |                 |
| socket                | 设置socke文件地址。/tmp/mysql.sock                           |      |                 |
| transaction_isolation | # 事务隔离级别。MySQL支持4种事务隔离级别，分别是： READ-UNCOMMITTED、READ-COMMITTED、REPEATABLE-READ、SERIALIZABLE<br ># 如没有指定，MySQL默认采用的是REPEATABLE-READ（此级别下可能参数很多间隙锁，影响性能，但是修改又影响主从复制及灾难恢复，建议还是修改代码逻辑吧），ORACLE默认的是READ-COMMITTED |      | REPEATABLE-READ |

数据库引擎与字符集相关设置

| 配置项                           | 说明                                                         | 值   | 默认值          |
| -------------------------------- | ------------------------------------------------------------ | ---- | --------------- |
| default_storage_engine           | 默认存储引擎。mysql 5.1 之后，默认引擎就是InnoDB             |      | InnoDB          |
| default_tmp_storage_engine       | 内存临时表默认引擎，默认InnoDB                               |      | InnoDB          |
| internal_tmp_disk_storage_engine | mysql 5.7新增特性，磁盘临时表默认引擎，默认InnoDB            |      | InnoDB          |
| character-set-server             | 数据库默认字符集,主流字符集支持一些特殊表情符号（特殊表情符占用4个字节） |      | utf8            |
| collation-server                 | 数据库字符集对应一些排序等规则，注意要和character-set-server对应 |      | utf8_general_ci |
| init_connect                     | 设置client连接mysql时的字符集,防止乱码                       |      |                 |
| lower_case_table_names           | 是否大小写敏感，默认值为0，1表示不敏感                       |      | 0               |

数据库连接相关设置

| 配置项              | 说明                                                         | 值   | 默认值           |
| ------------------- | ------------------------------------------------------------ | ---- | ---------------- |
| max_connections     | # 最大连接数，可设最大值16384。<br ># 如果服务器的并发连接请求量比较大，建议调高此值，以增加并行连接数量，当然这建立在机器能支撑的情况下，因为如果连接数越多，介于MySQL会为每个连接提供连接缓冲区，就会开销越多的内存，所以要适当调整该值，不能盲目提高设值。可以过'conn%'通配符查看当前状态的连接数量，以定夺该值的大小。<br ># 如果在访问时经常出现Too Many Connections的错误提示，则需要增大该参数值 |      |                  |
| max_connect_errors  | 最大错误连接数，如果有超出该参数值个数的中断错误连接，则该主机将被禁止连接。如需对该主机进行解禁，执行：`FLUSH HOST` 考虑高并发场景下的容错，建议加大 |      | 100              |
| open_files_limit    | # MySQL打开的文件描述符限制，默认最小1024<br ># 当open_files_limit没有被配置的时候，比较max_connections\*5和ulimit -n的值，哪个大用哪个，<br >#当open_file_limit被配置的时候，比较open_files_limit和max_connections\*5的值，哪个大用哪个。<br/># 注意：设置为65535 仍然可能出现报错信息 `Can't create a new thread；`此时观察系统 `cat /proc/mysql` 进程号 /limits，观察进程ulimit限制情况<br/># 过小的话，考虑修改系统配置表，`/etc/security/limits.conf` 和 `/etc/security/limits.d/90-nproc.conf` |      |                  |
| interactive_timeout | 服务器关闭交互式连接前等待活动的秒数。交互式客户端定义为在 mysql_real_connect() 中使用CLIENT_INTERACTIVE选项的客户端。 |      | 28800秒（8小时） |
| wait_timeout        | # 服务器关闭非交互连接之前等待活动的秒数。在线程启动时，根据全局wait_timeout值或全局interactive_timeout值初始化会话wait_timeout值<br ># 取决于客户端类型(由mysql_real_connect()的连接选项CLIENT_INTERACTIVE定义)。<br >#  MySQL服务器所支持的最大连接数是有上限的，因为每个连接的建立都会消耗内存，因此我们希望客户端在连接到MySQL Server处理完相应的操作后， <br ># 应该断开连接并释放占用的内存。如果你的MySQL Server有大量的闲置连接，他们不仅会白白消耗内存，而且如果连接一直在累加而不断开， <br >#  最终肯定会达到MySQL Server的连接上限数，这会报'too many connections'的错误。对于wait_timeout的值设定，应该根据系统的运行情况来判断。 <br ># 在系统运行一段时间后，可以通过show processlist命令查看当前系统的连接状态，如果发现有大量的sleep状态的连接进程，则说明该参数设置的过大， <br ># 可以进行适当的调整小些。要同时设置interactive_timeout和wait_timeout才会生效 |      |                  |
| back_log            | # MySQL能有的连接数量。当主要MySQL线程在一个很短时间内得到非常多的连接请求，这就起作用<br /># 然后主线程花些时间(尽管很短)检查连接并且启动一个新线程。back_log值指出在MySQL暂时停止回答新请求之前的短时间内多少个请求可以被存在堆栈中。 <br ># 如果期望在一个短时间内有很多连接，你需要增加它。也就是说，如果MySQL的连接数据达到max_connections时，新来的请求将会被存在堆栈中， 以等待某一连接释放资源，该堆栈的数量即back_log，如果等待连接的数量超过back_log，将不被授予连接资源。<br ># 另外，这值（back_log）局限于您的操作系统对到来的TCP/IP连接的侦听队列的大小。 <br ># 你的操作系统在这个队列大小上有它自己的限制（可以检查你的OS文档找出这个变量的最大值），试图设定back_log高于你的操作系统的限制将是无效的。<br ># 官方建议 back_log = 50 + (max_connections / 5) 封顶数为900 |      | 600              |

数据库数据交换设置

| 配置项             | 说明                                                         | 值   | 默认值 |
| ------------------ | ------------------------------------------------------------ | ---- | ------ |
| max_allowed_packet | 该参数限制服务器端，接受的数据包大小，如果有BLOB子段，建议增大此值，避免写入或者更新出错有BLOB子段，建议改为1024M。<br >增加该变量的值十分安全，这是因为只有当需要时才会分配额外内存。例如，仅当你发出长查询或MySQLd必须返回大的结果行时MySQLd才会分配更多内存。 |      |        |

内存，cache与buffer设置

| 配置项                   | 说明                                                         | 值   | 默认值 |
| ------------------------ | ------------------------------------------------------------ | ---- | ------ |
| tmp_table_size           | # MySQL的heap（堆积）表缓冲大小。所有联合在一个DML指令内完成，并且大多数联合甚至可以不用临时表即可以完成。 <br ># 大多数临时表是基于内存的(HEAP)表。具有大的记录长度的临时表 (所有列的长度的和)或包含BLOB列的表存储在硬盘上。 <br ># 如果某个内部heap（堆积）表大小超过tmp_table_size，MySQL可以根据需要自动将内存中的heap表改为基于硬盘的MyISAM表。还可以通过设置tmp_table_size选项来增加临时表的大小。也就是说，如果调高该值，MySQL同时将增加heap表的大小，可达到提高联接查询速度的效果 |      | 16M    |
| max_heap_table_size      | 定义了用户可以创建的内存表(memory table)的大小。这个值用来计算内存表的最大行数值。这个变量支持动态改变 |      |        |
| have_query_cache         | 表示这个mysql版本是否支持查询缓存。ps：`SHOW STATUS LIKE 'qcache%'`，与缓存相关的状态变量 |      |        |
| query_cache_type         | 这个系统变量控制着查询缓存功能的开启或关闭，0时表示关闭，1时表示打开，2表示只要select 中明确指定SQL_CACHE才缓存<br >看业务场景决定是否使用缓存，不使用，下面就不用配置了 |      | 0      |
| query_cache_size         | # 优点是查询缓冲可以极大的提高服务器速度, 如果你有大量的相同的查询并且很少修改表<br ># 缺点：在表经常变化的情况下或者如果查询原文每次都不同,查询缓冲也许引起性能下降而不是性能提升<br ># MySQL的查询缓冲大小（从4.0.1开始，MySQL提供了查询缓冲机制）使用查询缓冲，MySQL将SELECT语句和查询结果存放在缓冲区中， <br ># 今后对于同样的SELECT语句（区分大小写），将直接从缓冲区中读取结果。根据MySQL用户手册，使用查询缓冲最多可以达到238%的效率。 <br ># 通过检查状态值'Qcache_%'，可以知道query_cache_size设置是否合理：如果Qcache_lowmem_prunes的值非常大，则表明经常出现缓冲不够的情况， <br >#  如果Qcache_hits的值也非常大，则表明查询缓冲使用非常频繁，此时需要增加缓冲大小；如果Qcache_hits的值不大，则表明你的查询重复率很低， 这种情况下使用查询缓冲反而会影响效率，那么可以考虑不用查询缓冲。此外，在SELECT语句中加入SQL_NO_CACHE可以明确表示不使用查询缓冲 |      | 1M     |
| query_cache_limit        | 指定单个查询能够使用的缓冲区大小。只有小于此设定值的结果才会被缓冲，保护查询缓冲,防止一个极大的结果集将其他所有的查询结果都覆盖 |      | 1M     |
| query_cache_min_res_unit | # 每个被缓存的结果集要占用的最小内存，一般不怎么调整。<br/># 如果Qcache_free_blocks值过大，可能是query_cache_min_res_unit值过大，应该调小些<br/># query_cache_min_res_unit的估计值：`(query_cache_size - Qcache_free_memory) / Qcache_queries_in_cache` <br/> |      | 4kb    |
| binlog_cache_size        | # 在一个事务中binlog为了记录SQL状态所持有的cache大小<br/># 如果你经常使用大的,多声明的事务,你可以增加此值来获取更大的性能.<br/># 所有从事务来的状态都将被缓冲在binlog缓冲中然后在提交后一次性写入到binlog中<br/># 如果事务比此值大, 会使用磁盘上的临时文件来替代.<br/>#此缓冲在每个连接的事务第一次更新状态时被创建<br/> |      | 1M     |

日志文件相关设置

一般只开启三种日志，错误日志，慢查询日志，二进制日志。普通查询日志不开启。

| 配置项                                 | 说明                                                         | 值   | 默认值 |
| -------------------------------------- | ------------------------------------------------------------ | ---- | ------ |
| general_log                            | 普通查询日志，默认值off，不开启                              |      |        |
| general_log_file                       | 普通查询日志存放地址                                         |      |        |
| log_error_verbosity                    | *全局动态变量，范围：1～3*<br >表示错误日志记录的信息<br >1：只记录error信息；<br >2：记录error和warnings信息；<br >3：记录error、warnings和普通的notes信息。 |      | 3      |
| log_error                              | 错误日志文件地址                                             |      |        |
| slow_query_log                         | 开启慢查询                                                   |      |        |
| long_query_time                        | 开启慢查询时间，此处为1秒，达到此值才记录数据                |      |        |
| min_examined_row_limit                 | 检索行数达到此数值，才记录慢查询日志中                       |      |        |
| log_throttle_queries_not_using_indexes | mysql 5.6.5新增，用来表示每分钟允许记录到slow log的且未使用索引的SQL语句次数，默认值为0，不限制 |      | 0      |
| slow_query_log_file                    | 慢查询日志文件地址                                           |      |        |
| log-queries-not-using-indexes          | 开启记录没有使用索引查询语句                                 | 0/1  |        |
| log_bin                                | 开启二进制日志                                               |      |        |
| expire_logs_days                       | mysql清除过期日志的时间，默认值0，不自动清理，而是使用滚动循环的方式。 |      | 0      |
| max_binlog_size                        | 如果二进制日志写入的内容超出给定值，日志就会发生滚动。你不能将该变量设置为大于1GB或小于4096字节。 默认值是1GB |      |        |
| binlog_format                          | binlog日志格式。<br >binlog的格式也有三种：STATEMENT，ROW，MIXED。<br >mysql 5.7.7后，默认值从 MIXED 改为 ROW* |      | ROW    |
| sync_binlog                            | 默认值1，使binlog在每N次binlog写入后与硬盘同步，ps：1最慢    |      | 1      |

MyISAM 相关选项

| 配置项                     | 说明                                                         | 值   | 默认值 |
| -------------------------- | ------------------------------------------------------------ | ---- | ------ |
| key_buffer_size            | # 指定索引缓冲区的大小, 为MYISAM数据表开启供线程共享的索引缓存,对INNODB引擎无效。相当影响MyISAM的性能。<br/># 不要将其设置大于你可用内存的30%,因为一部分内存同样被OS用来缓冲行数据<br/># 甚至在你并不使用MyISAM 表的情况下, 你也需要仍旧设置起 8-64M 内存由于它同样会被内部临时磁盘表使用.<br/># 通过检查状态值Key_read_requests和Key_reads， # 可以知道key_buffer_size设置是否合理。比例key_reads/key_read_requests应该尽可能的低，至少是1:100，1:1000更好(上述状态值可以使用 `SHOW STATUS LIKE 'key_read%'` 获得)，<br ># 默认值 8M `建议值：对于内存在4GB左右的服务器该参数可设置为256M或384M`。<br>`注意：该参数值设置的过大反而会是服务器整体效率降低！`<br/> |      | 8M     |
| read_buffer_size           | # MySQL读入缓冲区大小,为每个扫描MyISAM的线程分配参数设置的内存大小缓冲区。 <br/># 默认值128kb，建议值：16G内存建议1M，4G：128kb或者256kb吧<br/># `注意，该缓冲区是每个连接独占的，所以总缓冲区大小为 128kb*连接数；极端情况128kb*maxconnectiosns，会超级大，所以要考虑日常平均连接数。`<br/># 一般不需要太关心该数值，稍微增大就可以了<br/> |      | 128kb  |
| read_rnd_buffer_size       | # MySQL的随机读缓冲区大小。支持任何存储引擎，适当增大，可以提高性能<br ># 默认值256kb；建议值：得参考连接数，16G内存，有人推荐8M <br ># `注意，该缓冲区是每个连接独占的，所以总缓冲区大小为128kb*连接数；极端情况128kb*maxconnectiosns，会超级大，所以要考虑日常平均连接数` |      | 256kb  |
| sort_buffer_size           | # MySQL执行排序使用的缓冲大小。order by或group by时用到，如果想要增加ORDER BY的速度，首先看是否可以让MySQL使用索引而不是额外的排序阶段。 如果不能，可以尝试增加sort_buffer_size变量的大小<br ># 支持所有引擎，innodb和myisam有自己的innodb_sort_buffer_size和myisam_sort_buffer_size设置<br ># `建议值：得参考连接数，16G内存，有人推荐8M.`<br ># `注意，该缓冲区是每个连接独占的，所以总缓冲区大小为 1M*连接数；极端情况1M*maxconnectiosns，会超级大。所以要考虑日常平均连接数。` |      | 256kb  |
| join_buffer_size           | # 联合查询操作所能使用的缓冲区大小，被使用来优化全联合(full JOINs 不带索引的联合)。和sort_buffer_size一样，该参数对应的分配内存也是每连接独享# 此缓冲<br/># 类似的联合在极大多数情况下有非常糟糕的性能表现,但是将此值设大能够减轻性能影响.<br/># 通过 “Select_full_join” 状态变量查看全联合的数量<br/># `建议值：16G内存，设置8M`<br/># `注意，该缓冲区是每个连接独占的，所以总缓冲区大小为 1M*连接数；极端情况1M*maxconnectiosns，会超级大。所以要考虑日常平均连接数。`<br/> |      | 256kb  |
| table_open_cache           | # 缓存linux文件描述符信息，加快数据文件打开速度。MySQL每打开一个表，都会读入一些数据到table_open_cache缓存中，当MySQL在这个缓存中找不到相应信息时，才会去磁盘上读取。<br  ># 假定系统有200个并发连接，则需将此参数设置为200*N(N为每个连接所需的文件描述符数目)； # 当把table_open_cache设置为很大时，如果系统处理不了那么多文件描述符，那么就会出现客户端失效，连接不上<br ># 它影响myisam表的打开关闭，但是不影响innodb表的打开关闭<br ># `建议值：根据状态变量Opened_tables去设定` |      | 2000   |
| table_definition_cache     | # 缓存表定义的相关信息，加快读取表信息速度 <br ># `最大值2000，建议值：基本不改` |      | 1400   |
| table_open_cache_instances | # 该参数是myssql 5.6后引入的，目的是提高并发。<br ># `建议值：cpu核数，并且<=16` |      | 1      |
| thread_cache_size          | # 当客户端断开之后，服务器处理此客户的线程将会缓存起来以响应下一个客户而不是销毁。可重用，减小了系统开销。<br/># 建议值：两种取值方式<br >方式一，根据物理内存，1G  —> 8；2G  —> 16； 3G  —> 32； >3G  —> 64；<br/>方式二，根据show status like  'threads%'，查看Threads_connected值。<br/> |      | 8      |
| thread_stack               | # `建议值：16/32G内存，512kb，其他一般不改变`<br ># 如果报错：Thread stack overrun，就增大看看 <br ># `注意，每个线程分配内存空间，所以总内存空间。。。` |      | 256k   |

innodb选项

| 配置项                              | 说明                                                         | 值     | 默认值                 |
| ----------------------------------- | ------------------------------------------------------------ | ------ | ---------------------- |
| innodb_page_cleaners                | 该参数可以提升扩展性和刷脏页性能<br >`建议值：4-8；并且必须小于innodb_buffer_pool_instances` |        | 1                      |
| innodb_page_size                    | 一般8k和16k中选择，8k的话，cpu消耗小些，selcet效率高一点，一般不用改 |        | 16k                    |
| innodb_buffer_pool_size             | InnoDB使用一个缓冲池来保存索引和原始数据, 不像MyISAM.这里你设置越大,你在存取表里面数据时所需要的磁盘I/O越少<br ># 在一个独立使用的数据库服务器上,你可以设置这个变量到服务器物理内存大小的60%-80% <br ># 注意别设置的过大，会导致system的swap空间被占用，导致操作系统变慢，从而减低sql查询的效率 <br ># `建议值：物理内存的60%-80%` |        | 128M                   |
| innodb_buffer_pool_instances        | # 只有当设置 innodb_buffer_pool_size 值大于1G时才有意义，小于1G，instances默认为1，大于1G，instances默认为8<br ># 但是网络上有评价，最佳性能，每个实例至少1G大小<br ># `默认值：1或8，建议值：innodb_buffer_pool_size/innodb_buffer_pool_instances >= 1G` |        | 8                      |
| innodb_buffer_pool_chunk_size       | # mysql 5.7 新特性。在线InnoDB缓冲池调整操作定义块大小<br ># 实际缓冲区大小必须为innodb_buffer_pool_chunk_size\*innodb_buffer_pool_instances\*倍数，取略大于innodb_buffer_pool_size <br ># 默认值128M，建议值：默认值就好，乱改反而容易出问题，它会影响实际buffer pool大小 |        | 128M                   |
| innodb_buffer_pool_load_at_startup  | 在启动时把热数据加载到内存。默认值为on，不修改               | on/off | on                     |
| innodb_buffer_pool_dump_at_shutdown | 在关闭时把热数据dump到本地磁盘。默认值为on，不修改           | on/off | on                     |
| innodb_lru_scan_depth               | 影响Innodb缓冲区的刷新算法，建议从小到大配置，直到zero free pages；innodb_lru_scan_depth * innodb_buffer_pool_instances <br/> |        | 1024                   |
| innodb_lock_wait_timeout            | 事务等待获取资源等待的最长时间，单位为秒，看具体业务情况，一般默认值就好<br >建议值：看业务 |        | 50                     |
| innodb_io_capacity                  | 设置了Mysql后台任务（例如页刷新和merge dadta from buffer pool）每秒io操作的上限<br >建议值：<br >方法一，单盘sata设100，sas10，raid10设200，ssd设2000，fushion-io设50000；<br >方法二，通过测试工具获得磁盘io性能后，设置IOPS数值/2。* |        | 200                    |
| innodb_io_capacity_max              | 该参数是所有缓冲区线程io操作的总上限<br >默认值：innodb_io_capacity的两倍。建议值：例如用iometer测试后的iops数值就好 |        |                        |
| innodb_flush_method                 | # 控制着innodb数据文件及redo log的打开、刷写模式，<br ># 三种模式：fdatasync(默认)，O_DSYNC，O_DIRECT# <br >- fdatasync：数据文件， buffer pool->os buffer->磁盘；日志文件，buffer pool->os buffer->磁盘；<br/> - O_DSYNC：数据文件，buffer pool->os buffer->磁盘；日志文件，buffer pool->磁盘；<br/>- O_DIRECT：数据文件，buffer pool->磁盘；                    日志文件，buffer pool->os buffer->磁盘；<br/># 默认值为空，建议值：使用SAN或者raid，建议用O_DIRECT，不懂测试的话，默认生产上使用O_DIRECT |        |                        |
| innodb_file_per_table               | InnoDB为独立表空间模式，每个数据库的每个表都会生成一个数据空间，mysql5.7之后默认开启<br >\# 独立表空间优点：<br >1．每个表都有自已独立的表空间。 <br >2．每个表的数据和索引都会存在自已的表空间中。 <br >3．可以实现单表在不同的数据库中移动。 <br >4．空间可以回收（除drop table操作处，表空不能自已回收） <br ># 缺点： 单表增加过大，如超过100G <br ># 结论： 共享表空间在Insert操作上少有优势。其它都没独立表空间表现好。当启用独立表空间时，请合理调整：innodb_open_files |        | 1                      |
| innodb_undo_directory               | InnoDB创建undo表空间的路径。通常等于undo log文件的存放目录。 |        | ./                     |
| innodb_undo_tablespaces             | InnoDB使用的undo表空间数量。等于undo log文件数量。5.7.21后开始弃用<br >建议默认值就好 |        | 0                      |
| innodb_undo_logs                    | 定义undo使用的回滚段数量。5.7.19后弃用。建议不动             |        | 128                    |
| innodb_undo_log_truncate            | 5.7.5后开始使用，在线收缩undo log使用的空间<br >建议值 1     | 0/1    | 0                      |
| innodb_max_undo_log_size            | 结合innodb_undo_log_truncate，实现undo空间收缩功能<br >建议值1G |        | 1G                     |
| innodb_log_group_home_dir           | 重作日志文件的存放目录                                       |        |                        |
| innodb_log_file_size                | 日志文件的大小<br >建议值：根据系统的磁盘空间和日志增长情况调整大小 |        | 48M                    |
| innodb_log_files_in_group           | 日志组中的文件数量，mysql以循环方式写入日志<br >默认值2，建议值：根据你系统的磁盘空间和日志增长情况调整大小 |        | 2                      |
| innodb_log_buffer_size              | 此参数确定写日志文件所用的内存大小，以M为单位。缓冲区更大能提高性能，但意外的故障将会丢失数据。MySQL开发人员建议设置为1－8M之间 |        |                        |
| innodb_flush_log_at_timeout         | 可以控制log从系统buffer刷入磁盘文件的刷新频率，增大可减轻系统负荷<br >建议值不改。系统性能一般够用。 |        | 1                      |
| innodb_flush_log_at_trx_commit      | 参数0：表示每秒将log buffer内容刷新到系统buffer中，再调用系统flush操作写入磁盘文件。<br/>参数1：表示每次事物提交，将log buffer内容刷新到系统buffer中，再调用系统flush操作写入磁盘文件。<br/>参数2：表示每次事物提交，将log buffer内容刷新到系统buffer中，隔1秒后再调用系统flush操作写入磁盘文件<br/> | 0/1/2  |                        |
| innodb_open_files                   | 限制Innodb能打开的表的数据，如果库里的表特别多的情况，请增加这个<br >`建议值：参考数据库表总数再进行调整，一般够用不用调整` |        | 2000                   |
| innodb_read_io_threads              | innodb处理io读写的后台并发线程数量，根据cpu核来确认，取值范围：1-64<br >`建议值：与逻辑cpu数量的一半保持一致。` |        | 4                      |
| innodb_write_io_threads             | 同上                                                         |        |                        |
| innodb_thread_concurrency           | 默认设置为 0,表示不限制并发数，这里推荐设置为0，更好去发挥CPU多核处理能力，提高并发量 |        | 0                      |
| innodb_purge_threads                | 默认值为4，建议不变。InnoDB中的清除操作是一类定期回收无用数据的操作。mysql 5.5之后，支持多线程清除操作 |        | 4                      |
| innodb_old_blocks_pct               | mysql缓冲区分为new blocks和old blocks；此参数表示old blocks占比<br >建议值，一般不动 |        | 37                     |
| innodb_old_blocks_time              | 新数据被载入缓冲池，进入old pages链区，当1秒后再次访问，则提升进入new pages链区 |        | 1000                   |
| innodb_use_native_aio               | 开启异步io，可以提高并发性，默认开启，建议不动               |        | 1                      |
| innodb_data_home_dir                | 默认为空，使用data目录，一般不改。                           |        |                        |
| innodb_data_file_path               | 定义InnoDB系统表空间数据文件的名称、大小和属性<br >默认值，不指定，默认为ibdata1:12M:autoextend |        | ibdata1:12M:autoextend |
| innodb_additional_mem_pool_size     | 设置了InnoDB存储引擎用来存放数据字典信息以及一些内部数据结构的内存空间大小，除非你的数据对象及其多，否则一般默认不改。 |        |                        |
| innodb_force_recovery               | 只有紧急情况需要恢复数据的时候，才改为大于1-6之间数值，含义查下官网 |        | 0                      |

### 其他设置

| 配置项                          | 说明                                                         | 值            | 默认值 |
| ------------------------------- | ------------------------------------------------------------ | ------------- | ------ |
| skip_name_resolve               | 禁止MySQL对外部连接进行DNS解析，使用这一选项可以消除MySQL进行DNS解析的时间<br >缺点：所有远程主机连接授权都要使用IP地址方式，因为只认得ip地址了 | 0/1           |        |
| explicit_defaults_for_timestamp | 默认值为off,timestamp列会自动更新为当前时间，设置为on\|1，timestamp列的值就要显式更新 | on/off<br>0/1 |        |

mysqldump

[mysqldump]

| 配置项             | 说明                                                         | 值   | 默认值 |
| ------------------ | ------------------------------------------------------------ | ---- | ------ |
| quick              | quick选项强制 mysqldump 从服务器查询取得记录直接输出而不是取得所有记录后将它们缓存到内存中 |      |        |
| max_allowed_packet |                                                              |      |        |

mysql命令行工具

[mysql]

| 配置项      | 说明                                                       | 值                         | 默认值 |
| ----------- | ---------------------------------------------------------- | -------------------------- | ------ |
| auto-rehash | mysql命令行工具不使用自动补全功能，建议还是改为auto-rehash | no-auto-rehash/auto-rehash |        |
| socket      |                                                            |                            |        |









# 参数调优 - 待完善