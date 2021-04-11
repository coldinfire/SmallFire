---
title: " MySQL基本操作 "
date: 2017-12-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - MySQL
---



### MySQL 数据库

#### 连接操作

| 操作     | 操作语句                                                     |
| -------- | ------------------------------------------------------------ |
| 连接     | mysql -h (ip) -u user_name -p password                       |
| 断开     | exit;                                                        |
| 创建授权 | grant select on 数据库.* to 用户名@登录主机 identified by "密码" |
| 删除授权 | revoke select,insert,update,delete om *.* from test2@localhost; |
| 修改密码 | mysql admin -u user_name -p old_pwd password new_pwd         |

#### 基本操作

| 操作           | 操作语句                | 操作   | 操作语句                                |
| :------------- | ----------------------- | :----- | --------------------------------------- |
| 显示数据库     | show databases;         | 创建库 | create database database_name;          |
| 使用库(选中库) | use database_name;      | 删除库 | drop database database_name;            |
| 显示数据表     | show tables;            | 创建表 | create table table_name (字段设定列表); |
| 使用表(选中表) | use table_name;         | 删除表 | drop table table_name;                  |
| 显示表结构     | describe table_name;    | 修改表 | alter table t1 rename t2;               |
| 清空表         | delete from table_name; | 查询表 | select * from table_name;               |

#### 复杂操作

| 操作                           | 操作语句                                                     |
| :----------------------------- | :----------------------------------------------------------- |
| 备份表                         | mysql binmysqldump -h(ip) -u root -p(password) databasename tablename > tablename.sql |
| 恢复表: (操作前先把原来表删除) | mysql binmysql -h(ip) -u root -p(password) databasename tablename < tablename.sql |
| 增加列                         | ALTER TABLE t2 ADD c INT UNSIGNED NOT NULL AUTO_INCREMENT,ADD INDEX (c); |
| 修改列                         | ALTER TABLE t2 MODIFY a TINYINT NOT NULL, CHANGE b c CHAR(20); |
| 删除列                         | ALTER TABLE t2 DROP COLUMN c;                                |
| 备份数据库                     | mysql binmysqldump -h(ip) -uroot -p(password) databasename > database.sql |
| 恢复数据库                     | mysql binmysql -h (ip) -u root -p(password) databasename < database.sql |
| 复制数据库                     | mysql binmysqldump --all -databases > all -databases.sql     |
| 修复数据库                     | mysql check -A -o -u root -p 54safer                         |
| 文本数据导入                   | load data local in file "file_name" into table table_name;   |

