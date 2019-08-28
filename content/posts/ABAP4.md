---
title: "ABAP 语法详解(SQL)"
date: 2018-05-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abap

---

### Open SQL

- ABAP可以通过两种方式与数据库交互

    - Native SQL:数据库自身的SQL，可以直接访问数据库，不够安全。


    - Open SQL:集成到ABAP中的标准SQL子集，通过SAP数据库接口识别不同的数据库，然后将语句进行转换成底层数据库对应的语言。

- 基本语法：与其他SQL语句类似，主要包括增删改查：Select、Update、Insert、Delete、Modify

  ​	<1> SELECT 语法  

  ```JS
  SELECT [SINGLE] <result> FROM <dbtab> ：SINGLE表示查询单行数据
         INTO <target>                ：内表或则结构
         [INTO <f1>...<fn>]           :将查询结果赋值到具体字段
         [INTO CORRESPONDING FILES OF <itab>]
                                      : 将查询结果按字段匹配赋值给具体的表或者结构体。
         WHERE <condition>            ：查询条件
         Group BY <fields>            ：分组查询条件
         ORDER BY <fields>.           ：排序条件 
  ```

  **表连接：**

  ```JS
  INNER JOIN：查询结果包含两个连接表中彼此相对应的数据记录。
  LEFT OUTER JOIN：查询结果集中包含左表中的所有数据记录，右表中仅查询出包含相匹配的数据。
  FULL OUTER JOIN：包含左右表所有的记录。
  ```


  以内表为查询条件：

  ​         `SELECT <f1...fn> FROM <dbtab> FOR ALL ENTRIED IN <itab> WHERE <cond>.`

  ​         使用 `FOR ALL ENTRIED IN itab` 前，一定要检查itab表是否为空，否则会造成SQL的执行效率极低。

  ​       获取限制读取数据的条数：前n条

​          	 `SELECT * FROM <dbtab> INTO <itab> UP TO <n> ROWS. `

  标准函数：

  ```JS
  COUNT()：统计查询总数。
  SUM()：统计表中某个数值字段的总和。
  AVG()：统计表中某个数值字段的平均值。
  MAX()：统计表中某个字段的最大值。
  MIN() ：统计表中某个字段的最小值。
  ```

  ​     <2> UPDATE（修改更新操作）

  ```JS
  UPDATE <dbtab> SET: f1...fn (WHERE <condition>).    
  UPDATE <dbtab> FROM TABLE <itab> (WHERE <condition>).
  ```

  ​     <3> INSERT(插入数据)

  ```JS
  INSERT INTO <dbtab> VALUES <conditin>.
  INSERT <dbtab> FROM TABLE <itab>.
  ```

  ​     <4> DELETE(删除操作)

  ```JS
  DELETE FROM <dbtab> WHERE <condition>.
  DELETE <dbtab> FROM TABLE <itab>.
  ```

  ​     <5> MODIFY(修改操作，相当于Update & Insert)    

  ```JS
  MODIFY <dbtab> FROM TABLE <itab>.
  ```

  

### Native SQL

​	EXEC SQL 和 ADBC 是所谓的 Native SQL，这种方式直接进入指定数据库，不涉及到 DBI，这样就没有 Table buffer。相对 EXEC SQL 来说，更推荐 ADBC 的方式执行 native sql，这种方式的好处是更加容易追踪错误。

**连接其他的数据库**：TCode:DBCO

​	添加新的连接：`MSSQL_SERVER=IP adress MSSQL_DBNAME=dbname OBJECT_SOURCE=dbname`

**程序调用**：

```json
DATA: p_dbname(10) VALUE 'DBNAME',
DATA: l_sql_error TYPE REF TO cx_sy_native_sql_error, 
      error_text TYPE string.
...
 内表数据准备
...
"连接数据库
TRY. 
 EXEC SQL. 
  CONNECT TO 'DBNAME'/ CONNECT TO :p_dbname
 ENDEXEC.
 EXEC SQL.
  SET CONNECTION 'DBNAME'
 ENDEXEC.
CATCH cx_sy_native_sql_error INTO l_sql_error. 
 CALL METHOD l_sql_error->get_text 
  RECEIVING 
   result = l_error_text. 
ENDTRY. 
IF sy-subrc <> 0.
 WRITE: /1 ‘连接到数据库失败:’, l_error_text.
 STOP. 
ENDIF.
"执行SQL
TRY. 
 LOOP AT gt_room INTO gs_room. 
  EXEC SQL. 
   insert into ljc_room ( room_id, room_name, room_people, room_desc ) 
   values(:gs_room-room_id, :gs_room-room_name, :gs_room-room_people, :gs_room-room_desc)
  ENDEXEC.
 ENDLOOP.
CATCH cx_sy_native_sql_error INTO l_sql_error. 
 error_text = l_sql_error->get_text( ). 
ENDTRY. 
"异常处理
IF l_error_text IS INITIAL.
 EXEC SQL.
  commit.
 ENDEXEC.
ELSE.
 CLEAR l_error_text. 
 EXEC SQL. 
  rollback 
 ENDEXEC.  
ENDIF.
"断开连接
EXEC SQL. 
 DISCONNECT :p_dbname 
ENDEXEC.
```



------

未完待续......[ABAP 语法详解(优化)](https://coldinfire.github.io/2018/ABAP5/)

