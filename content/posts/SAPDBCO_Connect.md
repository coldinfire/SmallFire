---
title: "SAP DBCO连接测试"
date: 2020-06-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - utils

---



TCode: DBCO查看数据库连接信息

![DBCO](/images/ABAP/DBCO1.png)

#### 测试连通性

使用程序：ADBC_TEST_CONNECTION / 参考 ADBC*

![SE38](/images/ABAP/DBCO3.png)

输入Database connection name,进行连接测试

![Connect test](/images/ABAP/DBCO2.png)

#### 连接SQLServer数据库

执行TCode:DBCO 新增连接数据，连接信息指定主机IP、数据库名即可

- MSSQL_SERVER=IP
- MSSQL_DBNAME=DatabaseName
- OBJECT_SOURCE=dbo

![DBCO SQLServer](/images/ABAP/DBCO4.png)

#### 连接DB2数据库

执行TCode:DBCO 新增连接数据，连接信息分别是数据库名、端口号、主机IP地址

- DB6_DB_NAME=DatabaseName;
- DB6_DB_SVCENAME=Port;
- DB6_DB_HOST=IP

![DBCO DB2](/images/ABAP/DBCO5.png)

#### 连接Oracle数据库

执行TCode:DBCO 新增连接数据，连接信息比较隐晦。

必须在 SAP 应用服务器上安装 Oracle Client，然后设置连接，并在这里将连接信息指定与连接名一致。
为了防止乱码，我们还应该在链接信息后加如下参数：ZHS16GBK
格式如下：ORCL.WORLD:ZHS16GBK

![DBCO Oracle](/images/ABAP/DBCO6.png)

#### 连接MaxDB数据库

执行TCode:DBCO 新增连接数据,MaxDB是SAP自己的数据库，则是 NetWeaver Developer 版中默认创建的一个连接

![DBCO MaxDB](/images/ABAP/DBCO7.png)