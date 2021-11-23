---
title: " SAP 数据库连接配置 "
date: 2020-06-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils

---

### 配置连接方式

**DBCO**：查看数据库连接信息

![DBCO](/images/ABAP/DBCO1.png)

**DB02**：DBA Cockpit 系统配置维护

![DB02](/images/ABAP/DBCO11.png)

**两种方式都是维护同一个配置表：DBCON** 。

#### 测试连通性

使用程序：`ADBC_TEST_CONNECTION` / 参考 ADBC*

![SE38](/images/ABAP/DBCO3.png)

输入 Database connection name，进行连接测试

![Connect test](/images/ABAP/DBCO2.png)

### 连接不同的数据库

#### 连接 SQL Server 数据库

执行事物码 DBCO 新增连接数据，连接信息指定主机IP、数据库名即可

- MSSQL_SERVER=IP
- MSSQL_DBNAME=DatabaseName
- OBJECT_SOURCE=dbo

![DBCO SQL Server](/images/ABAP/DBCO4.png)

#### 连接 DB2 数据库

执行事物码 DBCO 新增连接数据，连接信息分别是数据库名、端口号、主机IP地址

- DB6_DB_NAME=DatabaseName
- DB6_DB_SVCENAME=Port
- DB6_DB_HOST=IP

![DBCO DB2](/images/ABAP/DBCO5.png)

#### 连接 Oracle 数据库

执行事物码 DBCO 新增连接数据，连接信息比较隐晦。

必须在 SAP 应用服务器上安装 Oracle Client，然后设置连接，并在这里将连接信息指定与连接名一致。为了防止乱码，我们还应该在链接信息后加如下参数：ZHS16GBK
格式如下：ORCL.WORLD:ZHS16GBK。

![DBCO Oracle](/images/ABAP/DBCO6.png)

#### 连接 Max DB 数据库

执行事物码 DBCO 新增连接数据，Max DB 是 SAP 自己的数据库，则是 NetWeaver Developer 版中默认创建的一个连接

![DBCO Max DB](/images/ABAP/DBCO7.png)