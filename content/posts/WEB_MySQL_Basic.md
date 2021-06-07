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

| 操作               | 操作语句                                                    | 操作     | 操作语句                            |
| :----------------- | :---------------------------------------------------------- | :------- | :---------------------------------- |
| 创建库             | create database if not exists db_name character set 字符集; | 创建     | create table table_name (字段列表); |
| 查询数据库         | show databases;                                             | 显示     | show tables;                        |
| 库创建语句         | show create database db_name;                               | 查表结构 | desc table_name;                    |
| 修改数据库         | alter database db_name character set 字符集;                | 修改     | alter table t1 rename t2;           |
| 删除数据库         | drop database if exists db_name;                            | 删除     | drop table table_name;              |
| 查询当前使用数据库 | select database();                                          | 清空     | delete from table_name;             |
| 使用数据库         | use db_name;                                                |          |                                     |

数据库字段数据类型：

| 数据类型  | 字节数  | 语法               | 描述                                               |
| --------- | ------- | ------------------ | -------------------------------------------------- |
| char      | 0-255   | char(20)           | 定长字符串，存储右空格填充到指定的长度，默认1      |
| varchar   | 0-65535 | varchar(20)        | 变长字符串，创建VARCHAR类型字段必须定义长度        |
| int       | 4       | num int            | 整型数据类型，MySQL中整型默认是带符号的            |
| float     | 4       | num float(M,D)     | 单精度浮点型，显示长度(M)和小数位数(D)，默认(10,2) |
| double    | 8       | num double(M,D)    | 双精度浮点型，存在精度丢失问题，默认为(16,4)       |
| decimal   | M/D+2   | decimal(M, D)      | 定点型，以字符串形式进行保存，默认为decimal(10, 0) |
| date      | 3       | create_date date   | yyyy-MM-dd，存储日期值                             |
| time      | 3       | create_time time   | HH:mm:ss，存储时分秒                               |
| datetime  | 8       | create_on datetime | yyyy-MM-dd HH:mm:ss，存储日期+时间                 |
| timestamp | 4       | stmp timestamp     | yyyy-MM-dd HH:mm:ss，存储日期+时间，可作时间戳     |

#### DML 增删改表数据

添加数据

- INSERT INTO table_name(field1,field2,...) VALUES (field1_value,field2_value,...);
- INSERT INTO table_name VALUES(value1,value2,...);  -- 需要给所有列添加值
- 除了数字类型,NULL，其他类型需要使用引号引起来

删除数据

- DELETE FROM table_name WHERE condition_type;
- DELETE FROM table_name;   -- 清空表数据
- TRUNCATE TABLE table_name;  -- 删除表，然后再创建一个一模一样的空表

修改数据

- UPDATE table_name SET field1 = value1, field2 = value2 [WHERE condition_type];
- 如果不添加 where 条件，则会将表中所有记录全部修改

#### DQL 查询表数据



#### MySQL 复杂操作

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

