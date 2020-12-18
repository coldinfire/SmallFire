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

## Native SQL

EXEC SQL 和 ADBC 是所谓的 Native SQL，这种方式直接进入指定数据库，不涉及到 DBI，这样就没有 Table buffer。相对 EXEC SQL 来说，更推荐 ADBC 的方式执行 native sql，这种方式的好处是更加容易追踪错误。

**连接其他的数据库**：TCode : DBCO

存储数据表：DBCON

添加新的连接：`MSSQL_SERVER=IP adress MSSQL_DBNAME=dbname OBJECT_SOURCE=dbname`

注意事项：

- Native SQL 语句不能有结尾符号


- EXEC SQL ... ENDEXEC间不能有注释


- 参数占位符是冒号`：`


### **程序调用**

#### 连接DB

```html
DATA: CON_NAME LIKE DBCON-CON_NAME VALUE 'DBNAME',
DATA: sql_error TYPE REF TO cx_sy_native_sql_error, 
      error_text TYPE string.
...
 内表数据准备
...
" 连接数据库 "
TRY. 
  EXEC SQL. 
    CONNECT TO :CON_NAME
  ENDEXEC.
  EXEC SQL.
    SET CONNECTION 'CON_NAME'
  ENDEXEC.
  IF SY-SUBRC NE 0.
    error_text = 'Open Database Connection Error'.
    STOP.
  ENDIF.
ENDTRY. 
IF sy-subrc <> 0.
 error_text = 'Open Database Connection Error'.
 STOP. 
ENDIF.
```

#### 查询语句

```html
"执行SQL"
TRY.
  EXEC SQL PERFORMING frm_download_data.
    SELECT * into :wa_wik_emp FROM wik_emp
            WHERE pbnr = :PS_ISI-pbnr
  ENDEXEC.
  CATCH CX_SY_NATIVE_SQL_ERROR.
    CLEAR: WA_ISI.
    WA_ISI-TYPE = 'E'.
    WA_ISI-MESSAGE = 'Download RQM data Error'.
    MODIFY PT_ISI FROM WA_ISI INDEX P_INDEX TRANSPORTING TYPE MESSAGE .
    CLEAR: wa_isi.
ENDTRY.
```

#### 插入语句

```html
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
```

#### 异常处理和连接断开

```html
"异常处理"
IF error_text IS INITIAL.
 EXEC SQL.
  commit.
 ENDEXEC.
ELSE.
 CLEAR error_text. 
 EXEC SQL. 
  rollback 
 ENDEXEC.  
ENDIF.
"断开连接"
EXEC SQL. 
 DISCONNECT :p_dbname 
ENDEXEC.
```

### 游标使用

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

