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

### DDL  操纵数据库、表

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

### DML 增删改表数据

#### 添加数据

- INSERT INTO table_name(field1,field2,...) VALUES (field1_value,field2_value,...);
- INSERT INTO table_name VALUES(value1,value2,...);  -- 需要给所有列添加值
- 除了数字类型,NULL，其他类型需要使用引号引起来

#### 删除数据

- DELETE FROM table_name WHERE condition_type;
- DELETE FROM table_name;   -- 清空表数据
- TRUNCATE TABLE table_name;  -- 删除表，然后再创建一个一模一样的空表

#### 修改数据

- UPDATE table_name SET field1 = value1, field2 = value2 [WHERE condition_type];
- 如果不添加 where 条件，则会将表中所有记录全部修改

### DQL 查询表数据

SELECT 字段列表 FROM 表明列表 WHERE 条件列表;

-  [ GROUP BY 分组字段] [ HAVING 分组后条件] [ORDER BY 排序字段] [LIMIT 分页] 

- 去重 distinct：SELECT DISTINCT FROM table_name WHERE condition_type;

- NULL 值参与运算 `IFNULL(expr1,expr2)` : expr1 — 需要判断是否为 null 的字段，expr2 — null 值的替换值

#### WHERE 子条件的条件运算符

| 运算符类型     | 运算符                                                       |
| :------------- | :----------------------------------------------------------- |
| 比较运算符     | =、<> / !=、>、<、>=、<=                                     |
| 逻辑运算符     | && / AND 、\|\| / OR、! / NOT                                |
| 范围限定运算符 | (NOT) BETWEEN ... AND ... ，判断某个字段的值是否在给定的两个数据范围之间 |
| 范围包含运算符 | (NOT) IN (var1,var2,...)，判断某个字段的值是否存在指定的值列表中 |
| 模糊查询运算符 | (NOT) LIKE，判断某个字符字段的值是否包含给定的字符；%，代表任意字符；_，单个任意字符 |
| 存在运算符     | IS，判断一个字段值是否存在或不存在；只有两种写法  IS NULL / IS NOT NULL |

#### 排序查询

SELECT 字段列表 FROM 表明列表 WHERE 条件列表 ORDER BY 排序字段;

- 语法：ORDER BY 排序字段1 排序方式1, 排序字段2 排序方式2 ...
- 排序方式：ASC 默认值，升序；DESC 降序
- 多个排序条件：前面的条件值一样时，才会判断第二条件

#### 聚合函数

将一类数据作为一个整体，进行纵向的计算。

| 表达式 | 描述                       |              |                              |
| :----- | :------------------------- | :----------- | :--------------------------- |
| MAX    | 最大值                     | CONCAT       | 拼接字符串显示               |
| MIN    | 最小值                     | CONCAT_WS    | 使用统一符号拼接字段         |
| AVG    | 平均值                     | GROUP_CONCAT | 分组后的字段，拼接字符串显示 |
| COUNT  | 计算个数，选择非空列(主键) |              |                              |
| SUM    | 求和计算                   |              |                              |

#### 分组查询

SELECT 字段列表 FROM 表明列表 WHERE 条件列表 GROUP BY 分组字段 HAVING 分组后条件;

- 语法：GROUP BY 分组字段;
- 分组之后查询的字段：分组字段、聚合函数
- 使用 HAVING 进行分组后结果过滤；HAVING 分组后条件

HAVING 的语法格式与 WHERE 一致，但是两者使用方式不一样：

- WHERE 在分组之前进行限定，如果不满足条件则不参与分组；HAVING 是在分组之后进行的过滤，如果不满足条件，则不会被查询出来
- WHERE 不能用聚合函数，但是 HAVING 可以进行聚合函数的判断.

#### 分页查询

SELECT 字段列表 FROM 表明列表 WHERE 条件列表 LIMIT from_index,page_counts;

- 语法：LIMIT from_index,page_counts;  from_index — 开始索引位置，page_counts — 每页显示的条数
- 开始的索引 = (当前的页码 - 1) * 每页显示的条数

### 约束

对表中的数据进行限定，保证数据的正确性、有效性和完整性。

主键约束：`PRIMARY KEY` 要求被装饰的字段：唯一和非空 

- 删除主键：ALTER TABLE table_name DROP PRIMARY KEY;
- 创建表后添加主键：ALTER TABLE table_name MODIFY key_field INT PRIMARY KEY;

唯一约束：`UNIQUE` 要求被装饰的字段：唯一，联合唯一：在表创建结尾：`UNIQUE(字段1，字段2)`

- 删除唯一约束：ALTER TABLE table_name DROP INDEX unique_field;
- 创建表后添加唯一约束：ALTER TABLE table_name MODIFY unique_field VARCHAR(10) UNIQUE;

非空约束：`NOT NULL `要求被装饰的字段：非空 

- 删除非空约束：ALTER TABLE table_name MODIFY notnull_field VARCHAR(10);

- 创建表后添加非空约束：ALTER TABLE table_name MODIFY notnull_field VARCHAR(10) NOT NULL;

自动增加：`AUTO_INCREMENT` 自动增加，需要和主键 PRIMARY KEY 同时用 

- 删除自动增长：ALTER TABLE table_name MODIFY auto_key_field INT;
- 创建表后添加自增：ALTER TABLE table_name MODIFY auto_key_field INT AUTO_INCREMENT;

外键约束：`FOREIGN KEY` 某主表的外键

- CONSTRAINT 外键名称 FOREIGN KEY (外键字段名) REFERENCES 主表名称(主表列名称);

设置默认值：`DEFAULT` 为该属性设置默认值 

不足位数默认填充0：`ZEROFILL` 在int、char中使用  

### 数据库范式

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

