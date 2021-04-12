---
title: MySQL知识梳理-2.MySQL8.0部署
categories:
  - - SQL
tags:
  - MySQL
  - SQL
abbrlink: ea12c5be
date: 2021-03-11 10:36:49
top_img: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
cover: 'https://could-res-1252778021.file.myqcloud.com/pic/wallpaper/5ecce72a5977e.jpg'
---





# Linux（centos）系统部署

## 安装

安装包归档地址：https://downloads.mysql.com/archives/community/

### 下载

```
cd /usr/local/src
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz
```

### 解包

```
tar -Jxvf mysql-8.0.15-linux-glibc2.12-x86_64.tar.xz
mv mysql-8.0.15-linux-glibc2.12-x86_64 /usr/local/mysql8.0
```

### 配置

#### 创建账号

```
adduser mysql
```

#### 配置文件

```
vim /etc/my.cnf
```

配置文件内容

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



#### 创建所需的文件目录

```
cd /usr/local/mysql8.0
mkdir data sql_log undo
chown mysql:mysql -R data/ sql_log/ undo/
```



#### 配置启动命令

```
vim /etc/profile

# 文件末尾追加
export PATH=$PATH:/usr/local/mysql8.0/bin

# 保存退出  使配置生效
source /etc/profile
```



#### Mysql初始化

```
mysqld --initialize --user=mysql --basedir=/usr/local/mysql8.0 --datadir=/usr/local/mysql8.0/data
```



#### 启动命令

```
cd support-files/
cp mysql.server /etc/init.d/mysql
#启动mysql服务
/etc/init.d/mysql start
```



#### 注册服务开机自启

```
chmod +x /etc/init.d/mysql
chkconfig --add mysql
```

`systemctl status mysql` 可看到服务运行状态



#### 修改密码

##### 先找到默认生成的密码

```
cd /usr/local/mysql8.0/sql_log
grep password mysql-error.log
# 找到默认密码
```

##### 登录进mysql

```
mysql -u root -p

alter user user() identified by 'zhaoduo';
```

退出用新密码重新进即可



#### 设置允许远程访问

```
mysql -u root -p


use mysql;
# 查看用户信息
select host, user, authentication_string, plugin from user;
update user set host='%' where user='root';
```






