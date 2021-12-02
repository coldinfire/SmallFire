---
title: " ABAP Native SQL "
date: 2018-05-26
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abapbasis

---

EXEC SQL 和 ADBC 是所谓的 Native SQL，这种方式直接进入指定数据库，不涉及到 DBI，这样就没有 Table buffer。相对 EXEC SQL 来说，更推荐 ADBC 的方式执行 native sql，这种方式的好处是更加容易追踪错误。

### EXEC SQL

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
    SET CONNECTION 'CON_NAME'
  ENDEXEC.
  IF sy-subrc <> 0. "如果连接没有打开，打开连接"
    EXEC SQL. 
      CONNECT TO :CON_NAME
    ENDEXEC.
  ENDIF.
  IF sy-subrc <> 0.
    error_text = 'Open Database Connection Error'.
    STOP.
  ENDIF.
ENDTRY. 
```

#### 查询语句

```ABAP
"执行SQL：非游标方式"
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
FORM frm_download_data.
  APPEND wa_emp TO lt_emp.
ENDFORM.
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
  DISCONNECT :CON_NAME
ENDEXEC.
```

#### 游标使用

```ABAP
DATA: arg1 TYPE string VALUE '800'.
DATA: ls_data TYPE t001,
      lt_data TYPE TABLE t001.
" 打开游标 "
EXEC SQL.
  OPEN c1 FOR  
  SELECT MANDT, BUKRS FROM T001   " 远程数据库表 "
    WHERE MANDT = :arg1
ENDEXEC.
"循环通过游标读取记录"
"1.按字段顺序赋值，select 字段与 into 字段顺序必须一致"
"2.按结构整体赋值，select 字段必须与结构字段顺序一致且字段长度一致"
DO.
  EXEC SQL.
    FETCH NEXT c1 INTO :ls_data-mandt, :ls_data-bukrs
    "FETCH NEXT c1 INTO :ls_data"
  ENDEXEC.
  IF sy-subrc <> 0.
    EXIT.
  ELSE.
    APPEND ls_data TO lt_data.
  ENDIF.
ENDDO.
"关闭游标"
EXEC SQL.
  CLOSE c1     
ENDEXEC.
```

### 使用 ADBC 执行 Native SQL

SAP 提供了对原生 sql 的操作，主要有以下几个类组成：

- CL_SQL_STATEMENT - Execution of SQL Statements
- CL_SQL_PREPARED_STATEMENT - Prepared SQL Statements
- CL_SQL_CONNECTION - Administration of Database Connections
- CX_SQL_EXCEPTION - Exception Class for SQL Error

还会用到：

- CL_SQL_RESULT_SET - Resulting Set of an SQL Query
- CX_PARAMETER_INVALID - Superclass for Parameter Error

#### 程序实例

ABAP 标准 DEMO 程序：`ADBC_DEMO` 。

```ABAP
REPORT z_sql_demo.
"Get business datas"
"Excute data"
DATA: index TYPE i.
DATA: sql(100) TYPE c.
DATA: retcode  TYPE i,
      message  TYPE bapi_msg.
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
      msg    = message
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
*-------------------------------------------------------------
**Local Interface:
*  IMPORTING
*     REFERENCE(DBCONN) TYPE  C
*  EXPORTING
*     REFERENCE(RESULT) TYPE  I
*     REFERENCE(MSG)    TYPE BAPI_MSG
*  TABLES
*      SQL
*---------------------------------------------------------------
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
    CONCATENATE msg lv_root_message lv_sql_message INTO msg SEPARATED BY '/'.
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
      CONCATENATE msg lv_root_message lv_sql_message INTO msg SEPARATED BY '/'.
      MESSAGE msg TYPE 'I'.
      EXIT.
    CATCH cx_parameter_invalid INTO lr_cxpar.
      result = 1.
      lv_root_message = lr_cxpar->get_text( ).
      lv_param_message = lr_cxpar->parameter.
      CONCATENATE msg lv_root_message lv_sql_message INTO msg SEPARATED BY '/'.
      EXIT.
    ENDTRY.
  ENDLOOP.
  IF result = 1.
    CALL METHOD l_con_ref->rollback.
  ELSE.
    CALL METHOD l_con_ref->commit.
  ENDIF.
  
  GET REFERENCE OF ls_sp_test INTO l_dref.
  IF DBCONN <> 'DEFAULT'.
    CALL METHOD l_con_ref->close.
  ENDIF.
ENDFUNCTION.
```

