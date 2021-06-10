---
title: " MySQL高级操作 "
date: 2017-11-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - MySQL
---

### MySQL 事务操作

如果一个包含多个步骤的业务操作被事务管理，那么这些操作要么同时成功，要么同时失败。

事务执行是一个整体，所有 SQL 语句都必须执行成功。如果有一条 SQL 语句出现异常，则所有的 SQL 语句都要回滚，整个业务执行失败。

#### 操作语句

开启事务：`START TRANSACTION;`

回滚事务：`ROLLBACK;`

提交事务：`COMMIT; ` ，

事务提交方式：

- MySQL 数据库中事务默认自动提交，`SELECT @@autocommit;`
- 开启事务后，需要手动提交
- 修改事务提交方式：`SET @@autocommit = 0;` — 1 代表自动提交，0 代表手动提交

#### 事务的四大特征

| 特征   | 描述                                                 |
| :----- | :--------------------------------------------------- |
| 原子性 | 是不可分隔的最小操作单位，要么同时成功，要么同时失败 |
| 持久性 | 当事务提交或回滚后，数据库会持久化的保存数据         |
| 隔离性 | 多个事务之间是隔离的，彼此相互独立                   |
| 一致性 | 事务操作前后，数据总量保持不变                       |

### 事务的隔离级别

#### 存在问题

| 存在问题   | 描述                                                         |
| :--------- | :----------------------------------------------------------- |
| 脏读       | 一个事务，读取到另一个事务中没有提交的数据                   |
| 不可重复读 | 在同一个事务中，两次读取到的数据不一样                       |
| 幻读       | 一个事务操作数据表中所有记录，另一事务添加数据，则第一个事务查询不到添加修改 |

#### 隔离级别

隔离级别从小到大安全性越来越高，但是效率越来越低。

查询数据库隔离级别：`SELECT @@tx_isolation;`

设置当前 MySQL 连接的隔离级别：`SET TRANSACTION ISOLATION LEVEL level_field;` 

设置数据库系统全局的隔离级别：`SET GLOBAL TRANSACTION ISOLATION LEVEL 级别字符串;`

| 隔离级别              | 描述                                       |
| :-------------------- | :----------------------------------------- |
| READ UNCOMMITTED      | 读未提交；产生问题：脏读，不可重复读，幻读 |
| READ COMMITTED        | 读已提交；产生问题：不可重复读，幻读       |
| REPEATABLE READ(默认) | 可重复读；产生问题：幻读                   |
| SERIALIZABLE          | 串行化；可以解决所有问题                   |

### DCL 管理用户，授权

#### 管理用户

添加用户

- `CREATE USER 'username'@'hostname' IDENTIFIED BY 'password';`

删除用户

- `DROP USER 'username'@'hostname';`

修改用户

- `UPDATE USER SET PASSWORD = PASSWORD('new_password') WHERE USER = 'username';`
- `SET PASSWORD FOR 'username'@'hostname' = PASSWORD(new_password);`

查询用户

- 切换到 mysql 数据库：USE mysql;
- 查询 user 表：`SELECT * FROM user;`   `%` — 代表任意主机都可以访问

忘记 MySQL 密码？

- 停止 mysql 服务
- 使用无验证方式启动 mysql 服务：`mysqld --skip-grant-tables;`
- 打开新的 CMD 窗口，直接输入 mysql 命令 + 回车，可以登录成功；修改 root 密码 并重启 mysql 服务。

#### 授权管理

查看权限

- `SHOW GRANTS FOR 'username'@'hostname';`

授予权限

- `GRANT auth1,auth2,... ON database.table TO 'username'@'hostname';`
- 授予 ROOT 权限：`GRANT ALL ON *.* TO 'username'@'hostname';`

撤销权限

- `REVOKE auth1,auth2,... ON database.table FROM 'username'@'hostname';`