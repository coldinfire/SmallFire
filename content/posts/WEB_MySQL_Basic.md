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

#### 服务器启动和关闭

Windows：services.msc 查看服务信息，可以启动和关闭 MySQL 服务

command：net start mysql | net stop mysql ，启动和停止 MySQL 服务

#### 连接操作

| 操作     | 操作语句                                                     |
| :------- | :----------------------------------------------------------- |
| 连接     | mysql -h (ip) -u username -p password / mysql --user=username --password=psd |
| 断开     | exit; / quit;                                                |
| 创建授权 | grant select on 数据库.* to 用户名@登录主机 identified by "password" |
| 删除授权 | `revoke select,insert,update,delete om *.* from test2@localhost;` |
| 修改密码 | mysql admin -u username -p old_pwd password new_pwd          |

#### DDL  操纵数据库、表

| 操作               | 操作语句                                                     | 操作           | 操作语句                                |
| :----------------- | :----------------------------------------------------------- | :------------- | :-------------------------------------- |
| 创建库             | create database if not exists db_name character set 字符集;  # 默认 utf8 | 创建表         | create table table_name (字段设定列表); |
| 查询所有数据库     | show databases;                                              | 显示数据表     | show tables;                            |
| 查询数据库创建语句 | show create database db_name;                                | 查询表结构     | describe table_name;                    |
| 修改数据库         | alter database db_name character set 字符集;                 | 修改表         | alter table t1 rename t2;               |
| 删除数据库         | drop database if exists db_name;                             | 删除表         | drop table table_name;                  |
| 查询当前使用数据库 | select database();                                           | 清空表         | delete from table_name;                 |
| 使用数据库         | use db_name;                                                 | 使用表(选中表) | use table_name;                         |
|                    |                                                              | 查询表         | select * from table_name;               |

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

