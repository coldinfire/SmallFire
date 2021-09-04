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

#### 连接外部的数据库

- 事物码：`DBCO`

- 存储数据的表：`DBCON`


- 添加新的连接：`MSSQL_SERVER=IP adress MSSQL_DBNAME=dbname OBJECT_SOURCE=dbname`


#### 注意事项

- Native SQL 语句不能有结尾符号


- EXEC SQL ... ENDEXEC 间不能有注释


- 参数占位符是冒号`:para_value`


### 程序调用

#### 连接 DB

```ABAP
DATA: CON_NAME LIKE DBCON-CON_NAME VALUE 'DBNAME',
DATA: sql_error TYPE REF TO cx_sy_native_sql_error, 
      error_text TYPE string.
...
" 内表数据准备 "
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
```

#### 查询语句

```ABAP
"执行SQL"
LOOP demo_datas.
  TRY.
    EXEC SQL PERFORMING frm_download_data.
      SELECT * into :wa_emp FROM emp WHERE index = :demo_datas-index
    ENDEXEC.
    CATCH CX_SY_NATIVE_SQL_ERROR.
      CLEAR: e_type,e_message.
      e_type    = 'E'.
      e_message = 'Download RQM data Error'.
      CLEAR: wa_emp.
  ENDTRY.
ENDLOOP.
```

#### 插入语句

```ABAP
TRY. 
  LOOP AT gt_room INTO gs_room. 
    EXEC SQL. 
      insert into ljc_room ( room_id, room_name, room_people, room_desc ) 
      values(:gs_room-room_id, :gs_room-room_name, :gs_room-room_people, :gs_room-room_desc)
    ENDEXEC.
  ENDLOOP.
  CATCH cx_sy_native_sql_error INTO sql_error. 
    error_text = l_sql_error->get_text( ). 
ENDTRY. 
```

#### 异常处理和连接断开

```ABAP
"异常处理"
IF error_text IS INITIAL.
  EXEC SQL.
    commit
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

```ABAP
DATA: arg1 TYPE string VALUE '800'.
TABLES: t001.
EXEC SQL.
  OPEN c1 FOR  " 打开游标 "
  SELECT MANDT, BUKRS FROM T001   " 远程数据库表 "
    WHERE MANDT = :arg1 AND BUKRS >= 'ZA01'
ENDEXEC.
DO.
  EXEC SQL.
    FETCH NEXT c1 INTO :t001-mandt, :t001-bukrs  "读取游标"
  ENDEXEC.
  IF sy-subrc <> 0.
    EXIT.
  ELSE.
    WRITE: / t001-mandt, t001-bukrs.
  ENDIF.
ENDDO.
EXEC SQL.
  CLOSE c1     "关闭游标"
ENDEXEC.
```

### 使用类调用  Native SQL

#### 程序实例

```ABAP
REPORT z_sql_demo.
"Get business datas"
"Excute data"
DATA: index TYPE i.
DATA: sql(100) TYPE c.
DATA: retcode  TYPE i.
TYPES: BEGIN OF sql_data,
    sql(300) TYPE c,
END OF sql_data.
DATA: sql_datas TYPE STANDARD TABLE OF sql_data WITH HEADER LINE.
LOOP AT datas.
  index = sy-tabix.
  CLEAR: sql.
  CLEAR: sql_datas, sql_datas[].
  CONCATENATE 'INSERT INTO table(id,para1,para2,para3,para4)' 'values(' 
         INTO sql SEPARATED BY space.
  CONDENSE sql.
  CONCATENATE sql
    '''' index ''''        ','
    '1'                    ','
    '''' datas-para2 ''''  ','
    '''' datas-para3 ''''  ','
    '''' datas-para4 ''''             
    ')' INTO sql_datas-sql .
  APPEND sql_datas.
  CLEAR: retcode.
  CALL FUNCTION 'ZUU_EXEC_SQL'
    EXPORTING
      dbconn = 'CONN_NAME'
    IMPORTING
      result = retcode
    TABLES
      sql    = sql_datas.
  IF retcode = 0.
    "SQL Excute Success"
  ENDIF.
ENDLOOP.
```

#### 子程序：ZUU_EXEC_SQL

```ABAP
FUNCTION ZUU_EXEC_SQL .
*"-------------------------------------------------------------
*"*"Local Interface:
*"  IMPORTING
*"     REFERENCE(DBCONN) TYPE  C
*"  EXPORTING
*"     REFERENCE(RESULT) TYPE  I
*"  TABLES
*"      SQL
*"---------------------------------------------------------------
  TYPES: BEGIN OF t_sp_test,
    a TYPE char10,
  END OF t_sp_test,
  t_sp_test_tab TYPE TABLE OF t_sp_test.
  DATA: lt_sp_test TYPE t_sp_test_tab,
        ls_sp_test TYPE t_sp_test.
  DATA: lv_root_message TYPE string.
  DATA: lv_sql_message TYPE string.
  DATA: lv_param_message TYPE string.
  DATA: lr_cxsql TYPE REF TO cx_sql_exception.
  DATA: lr_cxpar TYPE REF TO cx_parameter_invalid.
  DATA: l_con_ref TYPE REF TO cl_sql_connection.
  DATA: l_stmt_ref TYPE REF TO cl_sql_statement.
  DATA: l_res_ref TYPE REF TO cl_sql_result_set.
  DATA: lv_stmt TYPE string.
  DATA: l_dref TYPE REF TO DATA,
        ncount TYPE I.
  TRY.
    CALL METHOD cl_sql_connection=>get_connection
      EXPORTING
        con_name = DBCONN
      RECEIVING
        con_ref = l_con_ref.
  CATCH cx_sql_exception INTO lr_cxsql.
    lv_root_message = lr_cxsql->get_text( ).
    lv_sql_message = lr_cxsql->sql_message.
    WRITE: / lv_root_message, / lv_sql_message.
  ENDTRY.
  result = 0.
  LOOP AT sql INTO lv_stmt.
    CALL METHOD l_con_ref->create_statement
      RECEIVING
        stmt_ref = l_stmt_ref.
    TRY.
      CALL METHOD l_stmt_ref->execute_update
        EXPORTING
          statement = lv_stmt
        receiving
          ROWS_PROCESSED = ncount.
    CATCH cx_sql_exception INTO lr_cxsql.
      result = 1.
      lv_root_message = lr_cxsql->get_text( ).
      lv_sql_message = lr_cxsql->sql_message.
      WRITE: / lv_root_message, / lv_sql_message.
      MESSAGE lv_stmt TYPE 'I'.
      EXIT.
    CATCH cx_parameter_invalid INTO lr_cxpar.
      result = 1.
      lv_root_message = lr_cxpar->get_text( ).
      lv_param_message = lr_cxpar->parameter.
      WRITE: / lv_root_message, / lv_param_message.
      EXIT.
    ENDTRY.
  ENDLOOP.
  IF result = 1.
    CALL METHOD l_con_ref->ROLLBACK.
  ELSE.
    CALL METHOD l_con_ref->COMMIT.
  ENDIF.
  GET REFERENCE OF ls_sp_test INTO l_dref.
  IF DBCONN <> 'DEFAULT'.
    CALL METHOD l_con_ref->CLOSE.
  ENDIF.
ENDFUNCTION.
```

