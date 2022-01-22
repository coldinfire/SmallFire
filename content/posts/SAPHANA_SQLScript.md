---
title: " SAP HANA SQLScript 使用 "
date: 2021-11-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - SAP Fiori

tags: 
  - HANA

---

SQLScript 是 SAP HANA 的专用 SQL 语言。SQLScript 是为 SAP HANA 数据库层的 SAP HANA 开发而设计的。SQLScript 在 code pushdown paradigm 转换中起着关键作用。

SQLScript 背后的动机是将数据密集型逻辑嵌入 SAP HANA 数据库本身，这称为 Code Pushdown：

![Code Pushdown](/images/HANA/HANA_CodePushdown.png)

#### 准备条件

在使用 SQLScript 进行 SAP HANA 开发之前，必须安装所需的软件并拥有适当的访问权限和权限：

- 拥有数据库开发人员账号，用来访问 SAP HANA 数据库
- 安装访问 SAP HANA 的开发者工具，例如 SAP HANA Studio 或 Eclipse
- 安装 SAP HANA 数据库开发插件，SAP HANA Tools --> SAP HANA Database Development

#### SQLScript 特点

SQLScript 是结构化查询语言 (SQL) 的扩展集合。 扩展包括：

- 数据扩展，允许在没有对应表的情况下定义表类型

- 功能扩展，允许定义（无副作用）函数，可用于表达和封装复杂的数据流
- 过程扩展，它提供在数据库进程上下文中执行的命令式构造。

SQLScript 特点

- 在 SQLScript 中，可以从单个 SQLScript 语句中获得多个结果集，而单个 SQL 语句将只返回一个结果集。
- 在 SQLScript 中也可以进行模块化； 也就是说，一组业务逻辑可以拆分为更小的代码片段，然后捆绑成可重用的逻辑语句组。这些组更具可读性和可理解性。
- 在 SQLScript 中可以使用像 IF/ELSE 语句这样的控制语句，但在常规命令行 SQL 中不可用。
- 在 SQLScript 中可以使用局部变量以及用于过渡结果的控制语句。

### DDL (Data Definition Language)

使用 DDL 定义底层关系数据库管理系统 (RDBMS) 的数据模型。 简单来说，DDL 定义了数据库的整个结构，负责创建表和修改表的表类型和存储类型（即列存储或行存储）。 表字段的域级约束可以保证表中只保存特定类型的数据。DDL 还负责确保维护表之间的关系完整性。 

要创建数据库对象，必须首先确定是否有权访问的 Catalog 文件夹下的正确 Schema。

#### Schema

Database Schema 是相关对象（如表、视图和存储过程）的逻辑分组。 如果没有 Schema 将无法写入 Database Catalog。SAP HANA 允许将 Schema 创建为设计时库中的可传输文件。

使用 `CREATE SCHEMA` 关键字创建 Schema：

- `CREATE SCHEMA <schema_name> [OWNED BY <user_name>]`

#### CREATE

表是关系数据库级别的最低数据存储形式。 视图和更高级别的数据库对象是在表之上创建的。

Create Table：

- 通过 SQL 语句创建

  ```ABAP
  CREATE COLUMN TABLE "<schema_name>"."<table_name>"( 
    "<column>" <type> not null,
    "<column>" <type> ,
    "<column>" <type> ,
    ...
  );
  ```

- 通过 HANA Studio 提供的工具视图创建

  ![Create Table](/images/HANA/HANA_SQLScript_1.png)

Create Table Type：借助 Table Type，SQLScript 可以处理并返回结果集的一堆行

- ```ABAP
  CREATE TYPE "<schema_name>"."<table_type_name>"( AS TABLE (
    "<column>" <type> not null,
    "<column>" <type> ,
    "<column>" <type> ,
    ...
  ));
  ```

#### ALTER

`ALTER TABLE "<schema_name>"."<table_name>"( "<column>" <type> ); `

#### DROP

`DROP TABLE "<schema_name>"."<table_name>";`

### DML (Data Manipulation Language)

当数据库模型创建后，数据将存储在数据库中的相关表和字段中。 DML 用于检索、存储、修改、删除、插入和更新数据库中的数据。 

#### SELECT

查询语句种，选择的列用逗号 (,) 分隔，并且语句以分号 (;) 结尾。

```ABAP
SELECT column1, column2, count(*)
  FROM "<schema_name>"."<table_name>"
 WHERE condition 
 GROUPBY column1,column2
 HAVING <Group_Conditon>
 ORDER BY column1 ASC, column2 DESC;
```

#### INSERT

- `INSERT INTO "<schema_name>"."<table_name>" VALUES('val1','val2',...,'valn');`

#### UPDATE

- `UPDATE "<schema_name>"."<table_name>" SET column1 = val1 ... ; `

#### DELETE 

### DCL (Data Control Language)

使用 DCL 管理数据库中数据的授权和控制。 使用 DCL 语句，可以创建角色并将其授予用户，以授予他们执行某些数据操作所需的权限。 GRANT 和 REVOKE 语句是授予和撤销用户权限的基本构建块。
使用查询语言，可以将实时对象保存在由引用到其他表的相关字段链接的二维表中。

### 存储过程 Stored Procedures

存储过程：是一组可执行语句，用于实现一组任务或操作。 在 SQLScript 的上下文中，可以将存储过程用作一种容器，而不是单独编写一组独立的 SQL 语句。使用存储过程的一些优点：

- 相关的 SQL 语句可以组织到一个容器中，专注于实现一组业务逻辑。
- 可以将这些过程进行参数化并多次调用。
- 可以将具有各种条件语句和循环语句的命令式逻辑放入过程中。
- 可以从 SAP HANA 系统外部远程调用这些过程。

#### Create Procedures

右键单击存储过程将保存的 Database Schema，打开 SQL 的命令提示符。 命令提示符打开后，使用 SQLScript 关键字 `CREATE PROCEDURE`。

```ABAP
CREATE [OR REPLACE] PROCEDURE <proc_name> [(<parameter_clause>)]
  [LANGUAGE <lang>] [SQL SECURITY <mode>] [DEFAULT SCHEMA <default_schema_name>]
  [READS SQLDATA] [WITH ENCRYPTION] [AUTOCOMMIT DDL ON|OFF]  AS 
BEGIN [SEQUENTIAL EXECUTION]
  <procedure_body>
END
```

#### 修改存储过程

```ABAP
ALTER PROCEDURE <proc_name> [(<parameter_clause>)]
  [LANGUAGE <lang>] [SQL SECURITY <mode>] [DEFAULT SCHEMA <default_schema_name>]
  [READS SQLDATA] [WITH ENCRYPTION] [AUTOCOMMIT DDL ON|OFF] AS
BEGIN [SEQUENTIAL EXECUTION]
  <procedure_body>
END
```

#### 调用存储过程

`CALL <proc_name> (<param_list>);`

#### 删除存储过程

`DROP PROCEDURE <proc_name>;`

### 用户自定义函数 UDFs(User-Defined Functions)

用户自定义函数也是 SQLScript 语句的集合，用于实现特定功能。

SQLScript 中存在两种类型的用户自定义函数：

- Scalar UDFs：返回值类型是 Scalar
- Table UDFs：返回值类型是 Table

#### UDFs Create

```ABAP
CREATE [OR REPLACE] FUNCTION <func_name> [(<parameter_clause>)] 
 RETURNS <return_type> [LANGUAGE <lang>] [SQL SECURITY <mode>]
 [DEFAULT SCHEMA <default_schema_name> [DETERMINISTIC]] [WITH ENCRYPTION] AS
BEGIN
  <function_body>
END
```

#### 删除 UDFs

`DROP FUNCTION <func_name> [<drop_option>];`

### SQLScript Constructs

SQLScript 编程通过在数据库层执行数据密集型操作（例如，命令式逻辑）来实现 Code Pushdown。在 SQLScript 中，可以创建局部变量、局部内表、条件语句和各种循环语句。

#### 本地变量

创建本地变量：`DECLARE <variable> <variable_type>;`

变量类型：

| 类型                   | 参数                                                       |
| ---------------------- | ---------------------------------------------------------- |
| Numeric types          | TINYINT、INT、BIGINT、DECIMAL、SMALL-DECIMAL、REAL、DOUBLE |
| Character string types | VARCHAR、NVARCHAR、ALPHANUM                                |
| Date-time types        | TIMESTAMP、SECOND DATE、DATE-TIME                          |
| Binary types           | VARBINARY                                                  |
| Large object types     | CLOB、NCLOB、BLOB                                          |
| Spatial types          | ST_GEOMETRY                                                |

### Arrays

#### 创建数组



### SQLScript Debug