---
title: Linux-Crontab
categories:
  - Linux
tags:
  - Linux
top_img: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218981853.jpg
cover: >-
  https://could-res-1252778021.cos.ap-shanghai.myqcloud.com/pic/wallpaper/1618218981853.jpg
abbrlink: d942e47f
date: 2021-10-21 10:57:09
---



# Crontab命令

查看列表

```sh
crontab -l
```

编辑crontab（用户级别）

```sh
crontab -e
```

编辑crontab（系统级别）

```
vim /etc/crontab
```

清空crontab

```sh
crontab -r
```

服务相关命令

```sh
sudo service crond start     #启动服务
sudo service crond stop      #关闭服务
sudo service crond restart   #重启服务
sudo service crond reload    #重新载入配置
sudo service crond status    #查看服务状态
```



# Crontab语法格式

## 表达式格式

```
*　　*　　*　　*　　*　　command
```

```
分　     时　     日　     月　       周　                 命令
00-59  00-23   01-31   01-12   0-6 (0 is sunday)
```

例子：每天凌晨1:00执行程序

```
0 1 * * * /root/wzfou.com/backup.sh
```

![img](https://could-res-1252778021.file.myqcloud.com/img/8bb2170665cc425caaeceb782019205c.jpg)





## crontab操作符

用来实现一些复杂的时间设定需要

| 操作符 | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| *      | 代表取值范围内的所有数字                                     |
| /      | 代表每的意思，每过多少个数字 ，代表每隔n长时间，"/5"表示每5个单位 |
| -      | 从X到Z ，代表一段时间范围                                    |
| ,      | 散列数字，代表分割开多个值                                   |



## 相关文件

**定时任务文件位置**

```
cd /var/spool/cron/
```

每个用户会有个对应用户名的文件



**运行日志文件**

```
ls /var/log/cron*
```

配置日志文件

```
vim /etc/rsyslog.conf 
```

在文件中找到 `#cron.` 一行`#cron.*  /var/log/cron.log` #号表示该行被注释掉了

去掉#号，后面是日志文件的全路径，改成：` cron.*   /var/log/cron.log ` 保存退出。 重新启动日志服务

```
service rsyslog restart
```







# **crontab的原理**

当使用者使用crontab这个命令来创建工作排程之后，该项工作就会被纪录到/var/spool/cron/里面去了，而且是以帐号来作为判别的喔！

举例来说， blue使用crontab后， 他的工作会被纪录到/var/spool/cron/blue里头去！但请注意，不要使用vi直接编辑该文件， 因为可能由於输入语法错误，会导致无法运行cron！

另外， cron运行的每一项工作都会被纪录到/var/log/cron这个登录项中，所以，如果你的Linux不知道有否被植入木马时，也可以搜寻一下 /var/log/cron这个登录项



**crontab的用户限制**

`/etc/cron.allow`：将允许使用 crontab 的用户写入其中，若不在这个文件内的使用者则不可使用 crontab；

`/etc/cron.deny`：将不允许使用 crontab 的用户写入其中，若未记录到这个文件当中的使用者，就可以使用 crontab







# crontab常用示例

| 表达式                                                       | 执行周期                                                   |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| \* * * * * /mnt/backup.sh                                    | 每1分钟执行一次                                            |
| 3,15 * * * * /mnt/backup.sh                                  | 每小时的第3和第15分钟执行一次                              |
| 3,15 8-11 * * * /mnt/backup.sh                               | 每天的8点到11点的第3和第15分钟执行一次                     |
| 3,15 8-11 */2 * * /mnt/backup.sh                             | 每隔两天的上午8点到11点的第3和第15分钟执行一次             |
| 3,15 8-11 * * 1 /mnt/backup.sh                               | 每周一上午8点到11点的第3和第15分钟执行一次                 |
| 30 21 * * * /mnt/backup.sh                                   | 每晚的21:30执行一次                                        |
| 45 4 1,10,22 * * /mnt/backup.sh                              | 每月1、10、22日的4 : 45执行一次                            |
| 10 1 * * 6,0 /mnt/backup.sh                                  | 每周六、周日的1 : 10执行一次                               |
| 0,30 18-23 * * * /mnt/backup.sh                              | 每天18 : 00至23 : 00之间每隔30分钟执行一次                 |
| 0 23 * * 6 /mnt/backup.sh                                    | 每星期六的晚上23: 00 pm执行一次                            |
| \* */1 * * * /mnt/backup.sh                                  | 每一小时执行一次                                           |
| \* 23-7/1 * * * /mnt/backup.sh                               | 每天晚上23点到第二天7点之间，每隔一小时执行一次            |
| @weekly /mnt/backup.sh                                       | 每个星期的第一天执行一次（即每个星期天晚上24：00开始执行） |
| 0 11 15 * * /mnt/backup.sh                                   | 每个月的15日执行一次                                       |
| @monthly /mnt/backup.sh                                      | 每个月的第一天执行一次（即每个月的1日凌晨0点开始执行）     |
| 0 0 * jan,apr,jun * /mnt/backup.sh                           | 在指定的月份执行一次（在1月,4月和 6月每天晚上0点执行一次） |
| @reboot /mnt/backup.sh                                       | 重启后执行一次                                             |
| MAILTO="raj"<br/>1 1 * * * /mnt/backup.sh                    | 定时任务执行后发一封邮件通知                               |
| SHELL=/bin/sh<br/>1 1 * * * /mnt/backup.sh                   | 指定shell （默认的是/bin/bash）                            |
| PATH=/sbin:/bin:/usr/sbin:/usr/bin<br/>1 1 * * * /mnt/backup.sh | 指定环境变量                                               |



# 常见问题

### 不立即生效

Crontab定时任务保存2分钟后才会执行

如果想要立即执行可以重启Crontab。当crontab失效时，可以尝试 `/etc/init.d/crond restart` 解决问题，或者查看日志看某个任务有没有执行/报错 `tail -f /var/log/cron`

### 没有执行权限

系统级任务调度与用户级任务调度。

只有 root 用户和 crontab 文件的所有者可以在 -e 、-l、-r 和 -v标志后面使用 *UserName* 以编辑、列出、除去或验证指定用户的 crontab 文件。

root用户的任务调度操作可以通过 `crontab –uroot –e`来设置，也可以将调度任务直接写入 `/etc/crontab` 文件。

想编辑别的用户的 crontab, 使用root运行下面的命令，同一个格式 (追加 “-u username” 到命令后) 也可以用来列出或删除 crontabs。

```
 crontab -u username -e
```











