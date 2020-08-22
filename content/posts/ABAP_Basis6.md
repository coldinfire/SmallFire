---
title: "ABAP Native SQL"
date: 2018-05-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

### Native SQL

​	EXEC SQL 和 ADBC 是所谓的 Native SQL，这种方式直接进入指定数据库，不涉及到 DBI，这样就没有 Table buffer。相对 EXEC SQL 来说，更推荐 ADBC 的方式执行 native sql，这种方式的好处是更加容易追踪错误。

**连接其他的数据库**：TCode:DBCO

​	添加新的连接：`MSSQL_SERVER=IP adress MSSQL_DBNAME=dbname OBJECT_SOURCE=dbname`

注意事项：

​	Native SQL 语句不能有结尾符号;

​	EXEC SQL ...... ENDEXEC间不能有注释;

​	参数占位符是冒号：;

**程序调用**：

```json
DATA: p_dbname(10) VALUE 'DBNAME',
DATA: l_sql_error TYPE REF TO cx_sy_native_sql_error, 
      error_text TYPE string.
...
 内表数据准备
...
"连接数据库"
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
"执行SQL"
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
"异常处理"
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
"断开连接"
EXEC SQL. 
 DISCONNECT :p_dbname 
ENDEXEC.
```

游标使用：

```JS
DATA : arg1 TYPE string VALUE '800' .
TABLES : t001 .
EXEC SQL .
  OPEN c1 FOR  " 打开游标"
  SELECT MANDT , BUKRS 
	FROM T001      " 远程数据库表 "
    WHERE MANDT = :arg1 AND BUKRS >= 'ZA01'
ENDEXEC .
DO .
  EXEC SQL .
    FETCH NEXT c1 INTO :t001-mandt, :t001-bukrs  "读取游标"
  ENDEXEC .
  IF sy - subrc <> 0 .
    EXIT .
  ELSE .
    WRITE : / t001-mandt, t001-bukrs .
  ENDIF .
ENDDO .
EXEC SQL .
  CLOSE c1 "关闭游标"
ENDEXEC .
```

------

未完待续......[ABAP 语法详解(优化)](https://coldinfire.github.io/2018/ABAP5/)

