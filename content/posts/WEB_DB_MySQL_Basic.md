---
title: " MySQL基本操作 "
date: 2017-11-23
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

Command：net start mysql | net stop mysql ，启动和停止 MySQL 服务

#### 连接操作

连接：`mysql -h (ip) -u username -p password`、`mysql --user=username --password=password`

断开：`exit; `、`quit;`

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

| 数据类型  | 字节数  | 语法          | 描述                                               |
| --------- | ------- | ------------- | -------------------------------------------------- |
| char      | 0-255   | char(20)      | 定长字符串，存储右空格填充到指定的长度，默认1      |
| varchar   | 0-65535 | varchar(20)   | 变长字符串，创建VARCHAR类型字段必须定义长度        |
| int       | 4       | int           | 整型数据类型，MySQL中整型默认是带符号的            |
| float     | 4       | float(M,D)    | 单精度浮点型，显示长度(M)和小数位数(D)，默认(10,2) |
| double    | 8       | double(M,D)   | 双精度浮点型，存在精度丢失问题，默认为(16,4)       |
| decimal   | M/D+2   | decimal(M, D) | 定点型，以字符串形式进行保存，默认为decimal(10, 0) |
| date      | 3       | date          | yyyy-MM-dd，存储日期值                             |
| time      | 3       | time          | HH:mm:ss，存储时分秒                               |
| datetime  | 8       | datetime      | yyyy-MM-dd HH:mm:ss，存储日期+时间                 |
| timestamp | 4       | timestamp     | yyyy-MM-dd HH:mm:ss，存储日期+时间，可作时间戳     |

JAVA数据类型和MYSQL数据类型对应关系表：

| JAVA 类型          | SQL 类型                  |
| :----------------- | :------------------------ |
| boolean            | BIT                       |
| byte               | TINYINT                   |
| short              | SMALLINT                  |
| int                | INTEGER                   |
| long               | BIGINT                    |
| String             | CHANR,VARCHAR,LONGVARCHAR |
| byte array         | BINARY VAR BINARY         |
| java.sql.Date      | DATE                      |
| java.sql.Time      | TIME                      |
| java.sql.Timestamp | TIMESTAMP                 |

### DML 增删改表数据

#### 添加数据

- INSERT INTO table_name(field1,field2,...) VALUES (field1_value,field2_value,...);
- INSERT INTO table_name VALUES(value1,value2,...);  -- 需要给所有列添加值
- 除了数字类型和 NULL，其他类型需要使用引号引起来

#### 删除数据

- DELETE FROM table_name WHERE condition;
- DELETE FROM table_name;   -- 清空表数据
- TRUNCATE TABLE table_name;  -- 删除表，然后再创建一个一模一样的空表

#### 修改数据

- UPDATE table_name SET field1 = value1, field2 = value2 [WHERE condition];
- 如果不添加 where 条件，则会将表中所有记录全部修改

### DQL 查询表数据

SELECT 字段列表 FROM 表明列表 WHERE 条件列表;

-  [ GROUP BY 分组字段] [ HAVING 分组后条件] [ORDER BY 排序字段] [LIMIT 分页] 

- 去重 distinct：SELECT DISTINCT FROM table_name WHERE condition;

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

#### 多表查询

完成多表查询，需要消除无用的数据。

内连接查询：从哪些表中查(tables)；条件是什么(condetion)，查询哪些字段(fields)

- 隐式内连接：使用 WHERE 条件消除无用数据
- 显示内连接：使用 [INNER] JOIN，两个表都满足该条件
  - SELECT 字段列表 FROM table1 [INNER] JOIN table2 ON condetion WHERE condetion;

外连接查询：

- 左外连接：使用 LEFT [OUTER] JOIN；查询的是左表所有数据以及其交集数据
  - SELECT 字段列表 FROM table1 LEFT [OUTER] JOIN table2 ON condetion WHERE condetion;
- 右外连接：使用 RIGHT [OUTER] JOIN；查询的是右表所有数据以及其交集数据
  - SELECT 字段列表 FROM table1 RIGHT [OUTER] JOIN table2 ON condetion WHERE condetion;

子查询：查询中嵌套查询，嵌套的查询内容为子查询

- 单行单列结果集的子查询
  - 子查询可以作为条件，使用运算符 ( >、<、>=、<=、= ) 进行判断 
- 多行单列结果集的子查询
  - 子查询可以作为条件，使用运算符 IN 进行判断
- 多行多列结果集的子查询
  - 子查询可以作为一张虚拟表参与查询；可以使用表连接替代

### 约束

对表中的数据进行限定，保证数据的正确性、有效性和完整性。

**主键约束** ：`PRIMARY KEY` 要求被装饰的字段：唯一和非空；联合主键：在表创建结尾，`PRIMARY KEY(field1,field2)`

- 删除主键：ALTER TABLE table_name DROP PRIMARY KEY;
- 创建表后添加主键：ALTER TABLE table_name MODIFY key_field INT PRIMARY KEY;

**唯一约束** ：`UNIQUE` 要求被装饰的字段：唯一；联合唯一：在表创建结尾，`UNIQUE(字段1，字段2)`

- 删除唯一约束：ALTER TABLE table_name DROP INDEX unique_field;
- 创建表后添加唯一约束：ALTER TABLE table_name MODIFY unique_field VARCHAR(10) UNIQUE;

**非空约束** ：`NOT NULL `要求被装饰的字段：非空 

- 删除非空约束：ALTER TABLE table_name MODIFY notnull_field VARCHAR(10);

- 创建表后添加非空约束：ALTER TABLE table_name MODIFY notnull_field VARCHAR(10) NOT NULL;

**自动增加** ：`AUTO_INCREMENT` 自动增加，需要和主键 PRIMARY KEY 同时用 

- 删除自动增长：ALTER TABLE table_name MODIFY auto_key_field INT;
- 创建表后添加自增：ALTER TABLE table_name MODIFY auto_key_field INT AUTO_INCREMENT;

**外键约束** ：`FOREIGN KEY` 某主表的外键

- CONSTRAINT fk_field_name FOREIGN KEY (外键字段名) REFERENCES 主表(主表列名称);
- 删除外键：ALTER TABLE table_name DROP FOREIGN KEY fk_field_name;
- 创建表后添加外键：ALTER TABLE table_name ADD CONSTRAINT fk_field_name FOREIGN KEY (外键字段名) REFERENCES 主表(主表字段名);
- 级联操作：在定义外键的语句后面添加 — 级联更新： `ON UPDATE CASCADE` ，级联删除： `ON DELETE CASCADE` 

设置默认值：`DEFAULT` 为该属性设置默认值

不足位数默认填充0：`ZEROFILL` 在int、char中使用

### 数据库范式

#### 多表之间的关系

| 表关系         | 实现方式                                                     |
| :------------- | :----------------------------------------------------------- |
| 一对一         | 可以在任意一方添加唯一外键指向另一方的主键，一般会合成到一张表 |
| 一对多(多对一) | 在多的一方建立外键，指向一的一方的主键                       |
| 多对多         | 添加中间表，至少包含两个字段，作为中间表的外键，分别指向两张表的主键 |

#### 规范

设计关系数据库时，遵循不同的规范要求，设计出合理的关系型数据库，这些不同的规范要求被称为不同的“范式”；各种范式呈递次规范，符合高一级范式的设计，必定符合低一级范式；越高的范式数据库冗余越小。

但是有些时候一昧的追求范式减少冗余，反而会降低数据读写的效率，这个时候就要反范式，利用空间来换时间。

范式分类：

| 范式                | 描述                                                         |
| :------------------ | :----------------------------------------------------------- |
| 第一范式(1NF)       | 每一列都是不可分割的原子数据项，与其它列名不重复             |
| 第二范式(2NF)       | 在 1NF 的基础上，非码属性必须完全依赖于候选码(消除了非主属性对于 key 的部分函数依赖) ；如果依赖于主键，则需要依赖于所有主键，不能存在依赖部分主键的情况 |
| 第三范式(3NF)       | 在 2NF 的基础上，任何非主属性不依赖其它非主属性(消除了非主属性对于 key 的传递函数依赖)；副键与副键之间，不能存在依赖关系 |
| 巴斯-科德范式(BCNF) | 在 3NF 的基础上，消除主属性对于码的部分与传递函数依赖        |
| 第四范式(4NF)       | 在 3NF 的基础上，消除表中的多值依赖                          |

依赖解析：

- 函数依赖：如果通过 A 属性(属性组)，可以确定唯一 B 属性的值，那么 B 依赖于 A。

- 完全函数依赖：如果 A 是一个属性组，则 B 属性值的确定需要依赖于 A 属性组中的所有的属性值。属性组是指多个字段。

- 部分函数依赖：如果 A 是一个属性组，则 B 属性值的确定需要依赖 A 属性组的某一些字段即可。

- 传递函数依赖：如果 A 属性(属性组)，可以确定唯一个 B 属性的值，再通过 B 属性的值又可以唯一确定 C 属性的值。

- 主键：在一张表中，一个属性(属性组)，被其他所有属性完全依赖，则称这个属性为该表的key(键值)。

#### MySQL 复杂操作

| 操作                           | 操作语句                                                     |
| :----------------------------- | :----------------------------------------------------------- |
| 备份数据库                     | mysqldump -h(ip) -uroot -p(password) databasename > database.sql |
| 恢复数据库                     | mysql -h (ip) -u root -p(password) databasename < database.sql |
| 复制数据库                     | mysqldump --all -databases > all -databases.sql              |
| 备份表                         | mysqldump -h(ip) -u root -p(password) databasename tablename > tablename.sql |
| 恢复表: (操作前先把原来表删除) | mysql binmysql -h(ip) -u root -p(password) databasename tablename < tablename.sql |
| 增加列                         | ALTER TABLE t2 ADD c INT UNSIGNED NOT NULL AUTO_INCREMENT,ADD INDEX (c); |
| 修改列                         | ALTER TABLE t2 MODIFY a TINYINT NOT NULL, CHANGE b c CHAR(20); |
| 删除列                         | ALTER TABLE t2 DROP COLUMN c;                                |
| 修复数据库                     | mysql check -A -o -u root -p 54safer                         |
| 文本数据导入                   | load data local in file "file_name" into table table_name;   |

