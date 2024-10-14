---
title: MySQL权限管理
date: 2018/02/02
tags: DBA
description: Some old articles may have been deprecated.
---

## 新建用户

以 root 用户登录 mysql ，创建 test 用户，并设置密码为 123456

``` plain
use mysql;
create user test;
update user set password=passworD("123456") where user='test';
```
## 赋予 test 用户相应权限

1. 允许在某指定 ip 服务器操作

所有权限，所有库

``` plain
grant all privileges on *.* to test@'<host>' identified by "123456" with grant option;</host>
```
所有权限，指定库 testdb

``` plain
grant all privileges on testdb.* to test@'<host>' identified by "123456" with grant option;</host>
```
1. 允许在任意服务器操作

所有权限，所有库

``` plain
grant all privileges on *.* to test@'localhost' identified by "123456" with grant option;
grant all privileges on *.* to test@'%' identified by "123456" with grant option;
```
所有权限，指定库 testdb

``` plain
grant all privileges on testdb.* to test@'localhost' identified by "123456" with grant option;
grant all privileges on testdb.* to test@'%' identified by "123456" with grant option;
```
- PS:

``` plain
all privileges 表示赋予除授权权限（ grant ）外所有权限
with grant option 同时授予其给其他用户授权（ grant ）的权限， grant 语句末尾去掉 with grant option 即表示不授予其 grant 权限
```
## 权限表：（使用以上命令，在 grant 后指定以下权限，可实现 “用户-登录客户端-权限” 控制）

|权限字段|作用域|权限详情
|------
|all privileges|服务器|除授权权限（ grant ）外所有权限
|USAGE|服务器|登录数据库权限
|CREATE|数据库、表或索引|创建数据库、表或索引权限
|DROP|数据库或表|删除数据库或表权限
|GRANT OPTION|服务器|赋予权限选项
|REFERENCES|数据库或表|将其它表的一个字段作为某一个表的外键约束
|ALTER|表|更改表，比如添加字段、索引等
|DELETE|表|删除数据权限
|INDEX|表|索引权限
|INSERT|表|插入权限
|SELECT|表|查询权限
|UPDATE|表|更新权限
|CREATE VIEW|视图|创建视图权限
|SHOW VIEW|视图|查看视图权限
|ALTER ROUTINE|存储过程|更改存储过程权限
|CREATE ROUTINE|存储过程|创建存储过程权限
|EXECUTE|存储过程|执行存储过程权限
|FILE|服务器|服务器主机上的文件访问
|CREATE TEMPORARY TABLES|服务器|创建临时表权限
|LOCK TABLES|服务器|锁表权限
|CREATE USER|服务器|创建用户权限
|PROCESS|服务器|查看进程权限
|RELOAD|服务器|执行flush-hosts, flush-logs, flush-privileges, flush-status, flush-tables, flush-threads, refresh, reload等命令的权限
|REPLICATION CLIENT|服务器|复制权限（客户端）
|REPLICATION SLAVE|服务器|复制权限（备用结点）
|SHOW DATABASES|服务器|查看数据库权限
|SHUTDOWN|服务器|关闭数据库权限
|SUPER|服务器|执行kill线程权限

## 查看用户权限

``` plain
# 查看当前用户
show grants;

# 查看指定用户（某用户某远程地址）
show grants for 'test'@'%';
```
## 回收权限（某用户某远程地址）

``` plain
revoke delete on *.* from 'test'@'localhost';
```
## 刷新权限表

``` plain
# 在执行以上权限更新命令后需要刷新权限表：
flush privileges;
```
## 用户重命名

``` plain
rename user 'test'@'%' to 'test2'@'%';
```
