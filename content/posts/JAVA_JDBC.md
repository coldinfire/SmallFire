---
title: " JDBC连接 "
date: 2017-12-03
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - JDBC
---

### JDBC 

JDBC (Java DataBase Connectivity )，Java 数据库连接，是Java程序访问数据库的标准接口。各个数据库厂商自己去实现该接口，并提供数据库驱动 jar 包。

使用JDBC的好处

- 各数据库厂商使用相同的接口，Java代码不需要针对不同数据库分别开发
- Java程序编译期仅依赖java.sql包，不依赖具体数据库的jar包
- 可随时替换底层数据库，访问数据库的Java代码基本不变

### JDBC 连接数据库

创建一个以JDBC连接数据库的程序，包含7个步骤。

- 获取JDBC所需的四个参数（user，password，url，driverClass）
- 加载JDBC驱动程序
- 创建数据库的连接 ，并判断连接是否成功
- 创建一个preparedStatement 
- 执行SQL语句
- 遍历结果集
- 处理异常，关闭JDBC对象资源