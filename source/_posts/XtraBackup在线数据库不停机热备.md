---
title: XtraBackup在线数据库不停机热备
date: 2018/01/31
tags: DBA
description: Some old articles may have been deprecated.
---

# 简介

转载一下主从同步和XtraBackup的简介：

## MySQL主从同步原理

MySQL主从同步是在MySQL主从复制(Master-Slave Replication)基础上实现的，通过设置在Master MySQL上的binlog(使其处于打开状态)，Slave MySQL上通过一个I/O线程从Master MySQL上读取binlog，然后传输到Slave MySQL的中继日志中，然后Slave MySQL的SQL线程从中继日志中读取中继日志，然后应用到Slave MySQL的数据库中。这样实现了主从数据同步功能。

## XtraBackup备份原理

innobackupex在后台线程不断追踪InnoDB的日志文件，然后复制InnoDB的数据文件。数据文件复制完成之后，日志的复制线程也会结束。这样就得到了不在同一时间点的数据副本和开始备份以后的事务日志。完成上面的步骤之后，就可以使用InnoDB崩溃恢复代码执行事务日志（redo log），以达到数据的一致性。

备份分为两个过程：

>
<ol>
- backup，备份阶段，追踪事务日志和复制数据文件（物理备份）。
- preparing，重放事务日志，使所有的数据处于同一个时间点，达到一致性状态。
</ol>


## XtraBackup的优点

>
<ol>
- 可以快速可靠的完成数据备份（复制数据文件和追踪事务日志）
- 数据备份过程中不会中断事务的处理（热备份）
- 节约磁盘空间和网络带宽
- 自动完成备份鉴定
- 因更快的恢复时间而提高在线时间
</ol>


# 操作

## 完整的步骤

>
<ol>
- 主、从服务器上都搭好MySQL服务，从服务器上MySQL版本大于等于主服务器，最好完全一致
- 在要做主从同步的服务器上分别安装XtraBackup
- 如果从服务器上有MySQL实例，停掉服务，备份删除数据库内容，保留数据库目录
- 配置主从服务器打开主从同步功能
- 主服务器上执行备份
- 传输备份文件到从服务器，并同步数据文件（apply-log）
- 从服务器上恢复备份
- 主服务器上授权同步帐号
- 从服务器上设置MASTER并开启同步
- 查看主从同步状态
</ol>


完成，可以检查同步状态了！

## 具体操作过程

``` plain
master host: 192.168.2.11
slave host: 192.168.2.12
```
### 一、主从服务器上搭建MySQL服务，并检查MySQL版本：(略)

### 二、主从服务器上分别安装XtraBackup，根据官方网站指导使用打包好的二进制，选择最新的稳定版2.4：

``` plain
# master &amp; slave
&gt; wget https://repo.percona.com/apt/percona-release_0.1-4.$(lsb_release -sc)_all.deb
&gt; sudo dpkg -i percona-release_0.1-4.$(lsb_release -sc)_all.deb
&gt; sudo apt-get update
&gt; sudo apt-get install percona-xtrabackup-24
```
### 三、停掉从服务器上MySQL服务，备份原有数据库，并删除原有数据库内容：(可选，如果从服务器是新机器可以跳过)

``` plain
&gt; mysqldump -u$USER -p$PASSWORD --routines --default-character-set=utf8 --locak-all-tables --add-drop-database -A dball.sql
&gt; sudo pkill mysql
&gt; sudo cd /data/mysql
# 下面这句千万别打错了，后果会很严重
&gt; sudo rm -rf *
```
### 四、配置MySQL打开主从同步功能

主服务器上编辑`/etc/mysql/my.conf`文件：

``` plain
[mysqld]
# 注意主从之间的server-id不能相同
server-id=1
log_bin=/var/log/mysql/mysql-bin.log
```
#### master my.cnf

``` plain
#
# The MySQL database server configuration file.
#
# You can copy this to one of:
# - "/etc/mysql/my.cnf" to set global options,
# - "~/.my.cnf" to set user-specific options.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# This will be passed to all mysql clients
# It has been reported that passwords should be enclosed with ticks/quotes
# escpecially if they contain "#" chars...
# Remember to edit /etc/mysql/debian.cnf when changing the socket location.
[client]
port=3306
socket=/var/run/mysqld/mysqld.sock
#user=zabbix
#host=localhost
#password=

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

# This was formally known as [safe_mysqld]. Both versions are currently parsed.
[mysqld_safe]
socket=/var/run/mysqld/mysqld.sock
nice=0

[mysqld]
#
# * Basic Settings
#
user=mysql
pid-file=/var/run/mysqld/mysqld.pid
socket=/var/run/mysqld/mysqld.sock
port=3306
basedir=/usr/local/mysql
datadir=/data/mysql
tmpdir=/tmp
#lc-messages-dir=/usr/share/mysql
skip-external-locking

max_connections=1000

event_scheduler=ON
max_allowed_packet=48M
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address=0.0.0.0
#
# * Fine Tuning
#
key_buffer=512M
max_allowed_packet=64M
thread_stack=192K
thread_cache_size=24
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover=BACKUP
#max_connections        = 100
#table_cache            = 64
#thread_concurrency     = 10
#
# * Query Cache Configuration
#
query_cache_limit=16M
query_cache_size=128M
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error log - should be very few entries.
#
log_error=/var/log/mysql/error.log
#
# Here you can see queries with especially long duration
#log_slow_queries    = /var/log/mysql/mysql-slow.log
#long_query_time = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id        = 1
#log_bin            = /var/log/mysql/mysql-bin.log
expire_logs_days=10
max_binlog_size=100M
#binlog_do_db        = include_database_name
#binlog_ignore_db    = include_database_name
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem

innodb_file_per_table=1
innodb_open_files=2048

innodb_log_group_home_dir=/data/mysql
innodb_buffer_pool_instances=24
innodb_buffer_pool_size=24G
innodb_additional_mem_pool_size=64M
innodb_log_file_size=64M
innodb_lock_wait_timeout=50
innodb_flush_log_at_trx_commit=0
### small swap / IO
innodb_flush_method=O_DIRECT
innodb_write_io_threads=16
innodb_read_io_threads=16
innodb_thread_concurrency=16
innodb_log_files_in_group=3
innodb_max_dirty_pages_pct=90

skip-name-resolve
slow_query_log
long_query_time=0.1

log-bin=mysql-bin
binlog_format=mixed
relay-log=relay-bin
relay-log-index=slave-relay-bin.index
#auto-increment-increment=2
#auto-increment-offset=1
server-id=1
log_slave_updates=1
read_only=0


[mysqldump]
quick
quote-names
max_allowed_packet=48M

[mysql]
#no-auto-rehash    # faster start of mysql but no tab completition

[isamchk]
key_buffer=16M
max_allowed_packet=48M

#
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
#
!includedir /etc/mysql/conf.d/
```
如果主服务器上MySQL是已经上线的系统，需要重启一下（实测/etc/init.d/mysql reload不起作用）：

``` plain
&gt; sudo pkill mysql
&gt; cd /usr/local/mysql
&gt; sudo bin/mysqld_safe --user=mysql --datadir=/data/mysql --pid-file=/var/run/mysqld/mysqld.pid &amp;
```
从服务器上编辑/etc/mysql/my.conf文件：

``` plain
[mysqld]
# 注意主从之间的server-id不能相同
server-id=2
# 最好设置从服务器为只读
# 注意：即使这里设置了只读，使用具有super权限的用户登录，也还是可以做写操作的
read_only=1
```
#### slave my.cnf

``` plain
#
# The MySQL database server configuration file.
#
# You can copy this to one of:
# - "/etc/mysql/my.cnf" to set global options,
# - "~/.my.cnf" to set user-specific options.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# This will be passed to all mysql clients
# It has been reported that passwords should be enclosed with ticks/quotes
# escpecially if they contain "#" chars...
# Remember to edit /etc/mysql/debian.cnf when changing the socket location.
[client]
port=3306
socket=/var/run/mysqld/mysqld.sock
#user=zabbix
#host=localhost
#password=

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

# This was formally known as [safe_mysqld]. Both versions are currently parsed.
[mysqld_safe]
socket=/var/run/mysqld/mysqld.sock
nice=0

[mysqld]
#
# * Basic Settings
#
user=mysql
pid-file=/var/run/mysqld/mysqld.pid
socket=/var/run/mysqld/mysqld.sock
port=3306
basedir=/usr/local/mysql
datadir=/data/mysql
tmpdir=/tmp
#lc-messages-dir=/usr/share/mysql
skip-external-locking

max_connections=1000

event_scheduler=ON
max_allowed_packet=48M
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address=0.0.0.0
#
# * Fine Tuning
#
key_buffer=512M
max_allowed_packet=64M
thread_stack=192K
thread_cache_size=24
# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover=BACKUP
#max_connections        = 100
#table_cache            = 64
#thread_concurrency     = 10
#
# * Query Cache Configuration
#
query_cache_limit=16M
query_cache_size=128M
#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
# Be aware that this log type is a performance killer.
# As of 5.1 you can enable the log at runtime!
#general_log_file        = /var/log/mysql/mysql.log
#general_log             = 1
#
# Error log - should be very few entries.
#
log_error=/var/log/mysql/error.log
#
# Here you can see queries with especially long duration
#log_slow_queries    = /var/log/mysql/mysql-slow.log
#long_query_time = 2
#log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
#server-id        = 1
#log_bin            = /var/log/mysql/mysql-bin.log
expire_logs_days=10
max_binlog_size=100M
#binlog_do_db        = include_database_name
#binlog_ignore_db    = include_database_name
#
# * InnoDB
#
# InnoDB is enabled by default with a 10MB datafile in /var/lib/mysql/.
# Read the manual for more InnoDB related options. There are many!
#
# * Security Features
#
# Read the manual, too, if you want chroot!
# chroot = /var/lib/mysql/
#
# For generating SSL certificates I recommend the OpenSSL GUI "tinyca".
#
# ssl-ca=/etc/mysql/cacert.pem
# ssl-cert=/etc/mysql/server-cert.pem
# ssl-key=/etc/mysql/server-key.pem

innodb_file_per_table=1
innodb_open_files=2048

innodb_log_group_home_dir=/data/mysql
innodb_buffer_pool_instances=24
innodb_buffer_pool_size=24G
innodb_additional_mem_pool_size=64M
innodb_log_file_size=64M
innodb_lock_wait_timeout=50
innodb_flush_log_at_trx_commit=0
### small swap / IO
innodb_flush_method=O_DIRECT
innodb_write_io_threads=16
innodb_read_io_threads=16
innodb_thread_concurrency=16
innodb_log_files_in_group=3
innodb_max_dirty_pages_pct=90

skip-name-resolve
slow_query_log
long_query_time=0.1

log-bin=mysql-bin
binlog_format=mixed
relay-log=relay-bin
relay-log-index=slave-relay-bin.index
#auto-increment-increment=2
#auto-increment-offset=1
server-id=2
log_slave_updates=1
read_only=0


[mysqldump]
quick
quote-names
max_allowed_packet=48M

[mysql]
#no-auto-rehash    # faster start of mysql but no tab completition

[isamchk]
key_buffer=16M
max_allowed_packet=48M

#
# * IMPORTANT: Additional settings that can override those from this file!
#   The files must end with '.cnf', otherwise they'll be ignored.
#
!includedir /etc/mysql/conf.d/
```
查询主从服务器状态：

``` plain
&gt; mysql -uroot -p -e "show global variables like 'server_id';"
    +---------------+-------+
    | Variable_name | Value |
    +---------------+-------+
    | server_id     | 1     |
    +---------------+-------+

&gt; mysql -uroot -p -e "show global variables like 'log_bin';"
    +---------------+-------+
    | Variable_name | Value |
    +---------------+-------+
    | log_bin       | ON    |
    +---------------+-------+
```
### 五、主服务器上执行备份操作

``` plain
&gt; sudo innobackupex --defaults-file=/etc/mysql/my.cnf --user=root --password=$PASSWORD --parallel=4 /tmp/mybackup
```
命令输出的最后几行通常类似这样：

innobackupex: Backup created in directory ‘/tmp/mybackup/2017-05-12_17-41-51’<br/>innobackupex: MySQL binlog position: filename ‘mysql-bin.000003’, position 1946<br/>111225 00:00:53 innobackupex: completed OK!

命令执行完在`/tmp/mybackup`目录下生成的`2017-05-12_17-41-51`目录，里面存储的是备份的数据，下一步要传输到从服务器上的即是这个文件夹。

输出中的`MySQL binlog position: filename 'mysql-bin.000003', position 1946`里面的两个数字，要记录以下，后面恢复到从服务器上的时候要用到。

### 六、传输并同步备份数据

读取备份数据需要ROOT权限，下面的命令需要使用sudo执行。

``` plain
&gt; sudo scp -r /tmp/mybackup git@192.168.2.12:/tmp/
```
在从服务器上执行：

``` plain
&gt; sudo innobackupex --apply-log /tmp/mybackup/2017-05-12
```
### 七、从服务器上恢复备份数据

``` plain
# 恢复数据
&gt; sudo innobackupex --defaults-file=/etc/mysql/my.cnf --user=root --password=$PASSWORD --copy-back /tmp/mybackup/2017-05-12/
# 需要恢复权限给mysql
&gt; sudo chown -R mysql:mysql /data/mysql
# 启动MySQL
&gt; cd /usr/local/mysql
&gt; sudo bin/mysqld_safe --user=mysql --datadir=/data/mysql --pid-file=/var/run/mysqld/mysqld.pid &amp;
```
### 八、主服务器上授权同步帐号

``` plain
&gt; mysql -u$USER -p$PASSWORD
&gt; create user slave@'192.168.2.12' identified by 'password';
&gt; grant replication slave on *.* to 'slave'@'192.168.2.12' identified by 'password';
&gt; flush privileges;
&gt; select distinct concat('User: ''',user,'''@''',host,''';') as query from mysql.user;
&gt; exit
```
倒数第二条语句查询当前数据库中的用户信息，检查slave是否在其中。

### 九、配置从服务器开启同步

``` plain
mysql -u$USER -p$PASSWORD
&gt; change master to
&gt; master_host = '192.168.2.11',
&gt; master_user = 'slave',
&gt; master_password = 'password',
&gt; master_port = 3306,
&gt; master_log_file = 'mysql-bin.000003',
&gt; master_log_pos = 1946;
&gt; start slave;
```
### 十、查看主从同步状态

``` plain
# master
&gt; mysql -u$USER -p -e "show master status \G;"
&gt; mysql -u$USER -p -e "show processlist \G;" | grep -i 'master'
# 第二条输出是否类似“State: Master has sent all binlog to slave; waiting for binlog to be updated”这样。

# slave
&gt; mysql -u$USER -p -e "show slave status \G;"
# 输出是否包含类似下面这样的语句:
    Slave_IO_State: Waiting for master to send event
    Slave_IO_Running: Yes
    Slave_SQL_Running: Yes
    Slave_SQL_Running_State: Slave has read all relay log; waiting for the slave I/O thread to update it

&gt; mysql -u$USER -p -e "show processlist \G;" | egrep -i '(master|slave)'
# 输出包含类似以下内容：
    State: Waiting for master to send event
    State: Slave has read all relay log; waiting for the slave I/O thread to update it
```
