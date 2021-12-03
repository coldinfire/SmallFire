---
title: " Spring JDBC 连接 "
date: 2017-11-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - JAVA

tags: 
  - MySQL

---

### Spring JDBC

Spring 框架对 JDBC 的简单封装。提供一个 JDBCTemplate 对象简化 JDBC 的开发

#### 使用步骤

Step1： [下载 spring-framework jar 包](https://repo.spring.io/webapp/#/artifacts/browse/tree/General/libs-release-local/org/springframework/spring) 并导入需要的 jar 包

Step2：创建 JdbcTemplate 对象。依赖于数据源 DataSource

- JdbcTemplate template = new JdbcTemplate(dataSource);

Step3：调用 JdbcTemplate 的方法完成 CRUD 的操作

- update()：执行 DML 语句，完成增、删、改操作
- queryForMap()：查询结果将结果集封装为 map 集合；查询的结果集长度只能是1；列名作为 key，值作为 value
- queryForList()：查询结果将结果集封装为 list 集合；将每一条记录封装为一个 Map 集合，然后将 Map 集合装载到 list 中
- query()：查询结果，将结果封装为 JavaBean 对象
  - 使用 `BeanPropertyRowMapper<T>(T.class)` 作为参数，可以完成数据到 JavaBean 的自动封装
- queryForObject：查询结果，将结果封装为对象
  - 一般用于聚合函数的查询

