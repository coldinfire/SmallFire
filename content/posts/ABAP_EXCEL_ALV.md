---
title: " 查询 DBCO 执行结果 "
date: 2021-07-23
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 查询 DBCO 执行结果

#### Step1：执行程序 `ADBC_QUERY`

- DB Connection Name：DBCO 中配置的数据库连接名称
- Table Name：DBCO 配置连接的数据库对应数据表的表名称

![ADBC Query](/images/ABAP/DBCO8.png)

#### Step2：输入和选择条件参数

- 在第一个文本区域输入查询的 Where 条件

- Selection of Table Fields：选择需要查询的结果集字段

![ADBC Query](/images/ABAP/DBCO9.png)

#### Step3：查看执行结果

![ADBC Query](/images/ABAP/DBCO10.png)