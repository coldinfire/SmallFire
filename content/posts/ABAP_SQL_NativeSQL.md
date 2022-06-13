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

每个数据库都有其原生结构化查询语言，也称为原生 SQL。 与数据库交互以检索结果集的方法有很多种，但本地 SQL 的执行是最快和最有效的。 这是因为原生 SQL 查询以自己的语言与数据库对话，并直接在数据库层执行。

EXEC SQL(Executing Native SQL Statements) 和 ADBC(ABAP Database Connectivity) 是所谓的 Native SQL，这种方式直接进入指定数据库，不涉及到 DBI，这样就没有 Table buffer。相对 EXEC SQL 来说，更推荐 ADBC 的方式执行 Native SQL，这种方式的好处是更加容易追踪错误。

![ABAP_SQL_Native](/images/ABAP/ABAP_SQL_Native.png)

#### 使用 ABAP 数据库连接时的注意事项

使用 ABAP 数据库连接的缺点是可能会出现语法错误，因为本机 SQL 查询是作为字符串传递的。 因此，必须始终使用 try –catch 块。 但是直到运行时才能确定这些错误。

事务控制语言命令不建议使用 ABAP 数据库连接类方法中的 DML 查询。 这些语句可用于 ABAP 数据库连接的单独逻辑工作单元，但应避免使用，SAP 不建议这样做。

### EXEC SQL

#### 连接外部的数据库

- 事物码：`DBCO`

- 存储数据的表：`DBCON`


- 添加新的连接：`MSSQL_SERVER=IP adress MSSQL_DBNAME=dbname OBJECT_SOURCE=dbname`

#### 注意事项

- Native SQL 语句不能有结尾符号


- `EXEC SQL ... ENDEXEC.` 间不能有注释
- 需要确定使用的第三方数据库中的表名和字段名是否区分大小写
- 通过在单引号 (' ') 中传递文字来对文字进行编码


- Native SQL 参数使用程序中的主变量时，与前面的冒号 (:) 一起传递`:para_value`

#### Native SQL 返回码

与 Open SQL 中一样，在 ENDEXEC 语句之后。SY-DBCNT 包含已处理的行数。 如果找到对应数据，则系统变量 SY-SUBRC 设置为 0； 如果不是，则将 SUBRC 变量设置为 4。

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
      CONNECT TO :CON_NAME [AS con]
    ENDEXEC.
  ENDIF.
  IF sy-subrc <> 0.
    error_text = 'Open Database Connection Error'.
    STOP.
  ENDIF.
ENDTRY. 
```

#### SELECT

```ABAP
"执行SQL：非游标方式"
LOOP demo_datas.
  TRY.
    EXEC SQL PERFORMING frm_download_data.
      SELECT * FROM emp into :wa_emp WHERE index = :demo_datas-index
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

#### SELECT SINGLE

```ABAP
DATA: p_fldate TYPE sy-datum.
TRY.
  EXEC SQL.
    SELECT carrid connid fldate price currency
      FROM sflight
      INTO :wa_sflight
     WHERE carrid = 'LH'
       AND connid = '0400'
       AND fldate =:p_fldate.
  ENDEXEC.
ENDTRY.
```

#### INSERT

```ABAP
TRY. 
  LOOP AT gt_room INTO gs_room. 
    EXEC SQL. 
      insert into ljc_room ( room_id, room_name, room_people, room_desc ) 
      values(:gs_room-room_id, :gs_room-room_name, :gs_room-room_people, :gs_room-room_desc)
    ENDEXEC.
  ENDLOOP.
  CATCH cx_sy_native_sql_error INTO sql_error. 
    error_text = sql_error->get_text( ). 
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
  OPEN cbcur FOR  
  SELECT MANDT, BUKRS FROM T001   " 远程数据库表 "
    WHERE MANDT = :arg1
ENDEXEC.
"循环通过游标读取记录"
"1.按字段顺序赋值，select 字段与 into 字段顺序必须一致"
"2.按结构整体赋值，select 字段必须与结构字段顺序一致且字段长度一致"
DO.
  EXEC SQL.
    FETCH NEXT dbcur INTO :ls_data-mandt, :ls_data-bukrs
  ENDEXEC.
  IF sy-subrc <> 0.
    EXIT.
  ELSE.
    APPEND ls_data TO lt_data.
  ENDIF.
ENDDO.
"关闭游标"
EXEC SQL.
  CLOSE cbcur     
ENDEXEC.
```

### 使用 ADBC 执行 Native SQL

不同与 EXEC SQL，ADBC 将查询内容准备为字符串，然后传递给 ABAP 数据库连接类的方法。

ADBC 主要类成员：这些类的方法可以将原生 SQL 查询传递给数据库执行，从而执行无法使用 Open SQL 执行的特定于数据库的命令。 所有 ABAP 数据库连接类都以 CL_SQL 开头，ABAP 数据库连接的异常类以 CX_SQL 开头。

| Class                        | Description                                                  |
| :--------------------------- | :----------------------------------------------------------- |
| CL_SQL_CONNECTION            | Administration of Database connection                        |
| CL_SQL_DEMO_UTIL             | Help class for ADBC_DEMO reports                             |
| CL_SQL_METADATA              | Method to describe database objects                          |
| CL_SQL_METADATA_ADA          | Implements CL_SQL_METADATA for SAP-supported databases       |
| CL_SQL_METADATA_ORA          | Implements CL_SQL_METADATA for Oracle                        |
| CL_SQL_PARAMETERS            | Administrates input and output parameters of SQL statements  |
| CL_SQL_PREPARED_STATEMENT    | A prepared SQL statement                                     |
| CL_SQL_STATEMENT             | Execution of SQL Statements，have various methods that can used in various scenarios |
| CL_SQL_RESULT_SET            | Resulting set of an SQL query                                |
| CX_SQL_EXCEPTION             | Exception class for SAP errors                               |
| CX_SQL_FEATURE_NOT_SUPPORTED | Exception class for SQL errors                               |
| CX_PARAMETER_INVALID         | Superclass for Parameter Error                               |

#### 使用步骤

连接数据库

- ```ABAP
  DATA:lo_sql_conn TYPE REF TO cl_sql_connection.
  DATA:lo_sql_stms TYPE REF TO cl_sql_statement,
       lv_statement TYPE string.
  DATA:lo_sql_result TYPE REF TO cl_sql_result_set.
  DATA:lr_data TYPE REFTO data.
  TRY.
    "Prepare Connection"
    lo_sql_conn = cl_sql_connection=>get_connection( ).
  CATCH cx_sql_exception .
  ENDTRY.
  ```

准备 SQL 语句

- `CL_LIB_SELTAB`：将Selection Option 转换成 where 条件

- ```ABAP
  *Convert selection option to a where clause string
  DATA: lo_seltab TYPE REF TO cl_lib_seltab,
        lv_where_cause_sel TYPE string.
  lo_seltab = cl_lib_seltab=>new( it_sel = s_carrid[] ).
  lv_where_clause_sel = lo_seltab->sql_where_condition( iv_field = 'CARRID' ).
  lv_statement = | SELECT | && |carrid,| && |connid,| && |fldate,| && |price,| && |currency|
                 && |FROM sflight|
                 && |WHERE mandt = '{ sy-mandt }'|
                 && |AND '{ lv_where_clause_sel }'|.
  ```

执行 SQL 语句 & 获取结果集

- ```ABAP
  TRY .
    "Prepare Connection"
    lo_sql_conn = cl_sql_connection=>get_connection( ).
    "Prepare the SQLStatement"
    lo_sql_stmt = lo_sql_conn->create_statement( ).
    "Execute Query"
    lo_sql_result = lo_sql_stmt->execute_query( lv_statement ).
    "Pass result set to the ref of the internal table"
    lo_sql_result->set_param_table( itab_ref = lr_data ).
    lo_sql_result->next_package( ).
  CATCH cx_sql_exception .
  ENDTRY.
  ```

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
    "连接到Database"
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

