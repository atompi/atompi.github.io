---
title: 安装 MySQL
date: 2018/01/05
tags: DBA
description: Some old articles may have been deprecated.
---

## 安装依赖环境

- Ubuntu

``` plain
sudo apt-get install -y cmake build-essential libreadline6-dev libncurses5-dev
```
## 获取 MySQL 安装包并解压

``` plain
wget http://mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-5.6/mysql-5.6.38.tar.gz
tar -zxf mysql-5.6.38.tar.gz
```
## 开始安装

``` plain
# 创建 mysql 用户
sudo groupadd mysql
sudo useradd -r -g mysql mysql

# 创建一些必要的目录并授权给 mysql 用户
sudo mkdir -p /etc/mysql/conf.d
sudo mkdir /run/mysqld
sudo mkdir -p /var/log/mysql
sudo mkdir -p /data/mysql
sudo mkdir -p /data/mysql-old-log
sudo chown -R mysql:mysql /var/log/mysql
sudo chown -R mysql:mysql /data/mysql
sudo chown -R mysql:mysql /data/mysql-old-log
sudo chown -R mysql:root /run/mysqld

# 编译安装
cd mysql-5.6.38/
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DMYSQL_DATADIR=/data/mysql -DSYSCONFDIR=/etc/mysql -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_MEMORY_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/var/run/mysqld/mysqld.sock -DMYSQL_TCP_PORT=3306 -DENABLED_LOCAL_INFILE=1 -DEXTRA_CHARSETS=utf8,gbk -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci -DWITH_PARTITION_STORAGE_ENGINE=1
make -j6
sudo make install
```
## 配置 MySQL

``` plain
# 修改安装目录权限
sudo chown -R mysql:mysql /usr/local/mysql

# 创建配置文件
sudo touch /etc/mysql/conf.d/.keepme
sudo vim /etc/mysql/my.cnf
```
`/etc/mysql/my.cnf` 文件内容如下

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
character_set_server = utf8
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
## 初始化 MySQL

``` plain
cd /usr/local/mysql
sudo scripts/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/data/mysql
```
## 创建软链接

``` plain
sudo ln -s /usr/local/mysql/bin/* /usr/bin
```
## 启动 MySQL

``` plain
sudo mysqld_safe --user=mysql --datadir=/data/mysql --pid-file=/var/run/mysqld/mysqld.pid &amp;
```
## 停止 MySQL

``` plain
sudo pkill -9 mysql
```
## 完成安装后的一些操作

``` plain
# 修改root密码
mysql -uroot
use mysql
update user set password=passworD("password") where user='root';

# 允许root(或者其他用户)远程登录（可选，个人开发环境可以开启）
grant all privileges on *.* to 'root'@'%' identified by 'password' with grant option;

# 删除匿名用户
delete from mysql.user where user='';

# 刷新权限表
flush privileges;
```
