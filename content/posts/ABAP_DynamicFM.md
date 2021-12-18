---
title: " ABAP 动态函数调用 "
date: 2021-10-28
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 调用语法

`CALL FUNCTION [PARAMETER-TABLE ptab] [EXCEPTION-TABLE etab].` ：通过特殊的内部表 ptab 和 etab 将实际参数分配给功能模块的形式参数，并将值返回给非基于类的异常。

#### 使用参数

PARAMETER-TABLE：用于为被调用函数模块的所有形参分配实参。 ptab 表的数据类型需要参照类型池 ABAP 的表类型 `abap_func_parmbind_tab` 或行类型 `abap_func_parmbind` 创建，类型为排序表。 列名称和种类构成了表 ptab 的唯一键。当语句 CALL FUNCTION 被执行时，对于每个非可选的形式参数，表格必须只包含一行。 对于每个可选的形式参数，这一行都是可选的。 表参数列表：

- NAME：函数模块的参数名称，类型 char30
- KIND：类型为 i，形式参数的类型。 kind 必须包含类型池 ABAP 的以下常量之一的值，如果从调用者角度指定的类型与形参的实际类型不匹配，则会引发可捕获异常 
  - `abap_func_exporting`：for input parameters value 10
  - `abap_func_importing`：for output parameters value 20
  - `abap_func_tables`：for table parameters value 30
  - `abap_func_changing`：for input/output parameters value 40
- VALUE：定义类型为`REF TO data`，作为指向实际参数的指针。 value 中的引用变量所指向的数据对象被分配给 name 中指定的形参。如果该引用变量类型与 FM 实际参数变量类型不一致，则引发异常  `CX_SY_DYN_CALL_ILLEGAL_TYPE`

EXCEPTION-TABLE：用于将返回值分配给未标记为异常类的被调用功能模块的异常。 etab 参照类型池 ABAP 的表类型 abap_func_excpbind_tab 或行类型 abap_func_excpbind 创建散列表。 当执行语句 CALL FUNCTION 时，该表可以为功能模块的每个非基于类的异常只包含一行。 表参数列表：

- NAME： 对于相应异常的名称，或 error_message，或以大写字母指定 OTHERS。类型 char30
- VALUE：类型为 i ，处理 name 中指定的异常后在 sy-subrc 中可用的数值
- MESSAGE：定义类型为 `REF TO data`

#### 简单实例

```ABAP
REPORT zdyn_call_fm.
TYPE-POOLS:abap,slis.
DATA: lv_func_name TYPE rs38l_fnam.
DATA: lv_msg TYPE string,
      lt_flight TYPE TABLE OF sflight.
DATA: lt_param TYPE abap_func_parmbind_tab,
      ls_param TYPE abap_func_parmbind,
      lt_excep TYPE abap_func_excpbind_tab,
      ls_excep TYPE abap_func_excpbind.
FIELD-SYMBOLS: <fs_msg> TYPE string.
               <fs_details> TYPE ANY TABLE.
PARAMETERS: p_carr TYPE sflight–carrid,
            p_conn TYPE sflight–connid,
            p_date TYPE sflight–fldate.
* Build parameters
ls_param–name = 'CARRID'.
ls_param–kind = abap_func_exporting.
GET REFERENCE OF p_carr INTO ls_param–value.
APPEND ls_param TO lt_param.
CLEAR ls_param.
ls_param–name = 'CONNID'.
ls_param–kind = abap_func_exporting.
GET REFERENCE OF p_conn INTO ls_param–value.
APPEND ls_param TO lt_param.
CLEAR ls_param.
ls_param–name = 'FLDATE'.
ls_param–kind = abap_func_exporting.
GET REFERENCE OF p_date INTO ls_param–value.
APPEND ls_param TO lt_param.
CLEAR ls_param.
ls_param–name = 'MSG'.
ls_param–kind = abap_func_importing.
GET REFERENCE OF lv_msg INTO ls_param–value.
APPEND ls_param TO lt_param.
CLEAR ls_param.
ls_param–name = 'DETAILS'.
ls_param–kind = abap_func_tables.
GET REFERENCE OF lt_flight INTO ls_param–value.
APPEND ls_param TO lt_param.
CLEAR ls_param.
"Exception Parameter"
ls_excep-name = 'PROGRAM_ERROR'.
ls_excep-value = 10.
INSERT ls_excep INTO TABLE lt_excep.
* Dynamic FM call with all import,export,table parameters in the Parameter-table
CALL FUNCTION lv_func_name
  PARAMETER–TABLE 
    lt_param
  EXCEPTION-TABLE 
    lt_excep.
* FM Result
READ TABLE lt_param INTO ls_param WITH KEY name = 'MSG'.
IF sy–subrc IS INITIAL.
  ASSIGN ls_param–value->* TO <fs_msg>.
  IF <fs_msg> IS ASSIGNED AND <fs_msg> IS NOT INITIAL.
    WRITE:/ <fs_msg>.
  ELSE.
    READ TABLE lt_param INTO ls_param WITH KEY name = 'DETAILS'.
    IF sy–subrc IS INITIAL.
      ASSIGN ls_param–value->* TO <fs_details>.
      IF <fs_details> IS ASSIGNED AND <fs_details> IS NOT INITIAL .
        lt_flight =   <fs_details>.
        CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY'
          EXPORTING
            i_callback_program = sy–repid
            i_structure_name   = 'SFLIGHT'
          TABLES
            t_outtab           = lt_flight.
      ENDIF.
    ENDIF.
  ENDIF.
ENDIF.
```

### 获取 Function Module 参数信息

#### 函数：RPY_FUNCTIONMODULE_READ_NEW

![FUNCTION MODULE](/images/ABAP/FM_Detail0.png)

参数详情

- IMPORT_PARAMETER

  ![FUNCTION MODULE](/images/ABAP/FM_Detail1.png)

- EXPORT_PARAMETER

  ![FUNCTION MODULE](/images/ABAP/FM_Detail2.png)

- TABLES_PARAMETER

  ![FUNCTION MODULE](/images/ABAP/FM_Detail3.png)

参数判断：通过反射方法判断类型

```ABAP
DATA: lo_tabledescr  TYPE REF TO cl_abap_tabledescr,
      lo_structdescr TYPE REF TO cl_abap_structdescr,
      lo_elemdescr   TYPE REF TO cl_abap_elemdescr,
      lv_linetypename TYPE string,
      t_tabdescr      TYPE abap_compdescr_tab.
LOOP AT lt_chg_para.
  TRY. "如果当前try成功则为Table type，失败则执行下一个try语句判断Structure type"
*Note: To create a table in Changing Parameter, the type should be a dictionary table type.
      lo_tabledescr   ?= cl_abap_typedescr=>describe_by_name( lt_chg_para-dbfield ).
      lo_structdescr  ?= lo_tabledescr->get_table_line_type( ).
      lv_linetypename = lo_structdescr->get_relative_name( ).
      lo_structdescr ?= cl_abap_typedescr=>describe_by_name( lv_linetypename ).
      t_tabdescr[]  =  lo_structdescr->components[].
    CATCH cx_root.
      TRY."如果当前try成功则为structure type."
          lo_structdescr ?= cl_abap_typedescr=>describe_by_data( lt_chg_para-dbfield ).
          t_tabdescr[] = lo_structdescr->components[].
        CATCH cx_root. 
          "如果上面两个都发生异常，则为variable type"
          lo_elemdescr ?= cl_abap_typedescr=>describe_by_name( lt_chg_para-dbfield ).
      ENDTRY.
  ENDTRY.
ENDLOOP.
```

调用函数

```ABAP
DATA: lv_func_name TYPE RS38L_FNAM.
DATA: lt_imp_para TYPE TABLE OF rsimp WITH HEADER LINE,
      lt_chg_para TYPE TABLE OF rscha WITH HEADER LINE,
      lt_exp_para TYPE TABLE OF rsexp WITH HEADER LINE,
      lt_tab_para TYPE TABLE OF rstbl WITH HEADER LINE,
      lt_exc_list TYPE TABLE OF rsexc WITH HEADER LINE,
      lt_document TYPE TABLE OF rsfdo WITH HEADER LINE,
      lt_source TYPE TABLE OF rssource WITH HEADER LINE.
CLEAR:lt_rsimp,lt_rscha,lt_rsexp,lt_rstbl,lt_rsexc,lt_rsfdo,lt_source.
REFRESH:lt_rsimp,lt_rscha,lt_rsexp,lt_rstbl,lt_rsexc,lt_rsfdo,lt_source.
lv_func_name = 'BAPI_PO_CREATE'.
CALL FUNCTION 'RPY_FUNCTIONMODULE_READ_NEW'
  EXPORTING
    functionname       = lv_func_name
  TABLES
    import_parameter   = lt_imp_para
    changing_parameter = lt_chg_para
    export_parameter   = lt_exp_para
    tables_parameter   = lt_tab_para
    exception_list     = lt_exc_list
    documentation      = lt_document
    source             = lt_source.
```

#### 函数：RFC_GET_FUNCTION_INTERFACE_US

![RFC_GET_FUNCTION_INTERFACE_US](/images/ABAP/FM_Detail4.png)

![RFC_GET_FUNCTION_INTERFACE_US](/images/ABAP/FM_Detail5.png)

参数详情

| Parameter  | Description                       | Parameter | Parameter | Parameter |
| :--------- | --------------------------------- | :-------- | :-------- | :-------- |
| PARAMCLASS | i (import)、e (export)、T (table) | PARAMETER | TABNAME   | FIELDNAME |

PARAMCLASS  参数判断

- I 或则 E：去 DD02L 表中根据 TABNAME 查找(TRANSP 透明表、INTTAB 结构) ，如果类型不是 TRANSP
  - 如果类型是 INTTAB，那么 PARAMETER 就是一个 structure
  - 如果 DD02L 表中不存在或则 FIELDNAME 不为空，那么 PARAMETER 是一个 variable；

- I 或则 E 并且 TABNAME 的类型是 table 那么 PARAMETER 就是一个 table
  - DD40VV / DD40L 判断 table 实际类型，是否参照 ROWTYPE

- T 代表 PARAMETER 是一个 table

#### 动态实例

```ABAP
REPORT zdyn_call_fm.
TYPE-POOLS:abap,slis.
DATA: lv_func_name TYPE rs38l_fnam.
DATA: lv_msg TYPE string.
DATA: lt_param TYPE abap_func_parmbind_tab,
      ls_param TYPE abap_func_parmbind,
      lt_excep TYPE abap_func_excpbind_tab,
      ls_excep TYPE abap_func_excpbind.
FIELD-SYMBOLS: <fs_msg> TYPE string.
DATA:BEGIN OF gs_data,
  canum          TYPE HRFPM_NO_DOC_LINES,
  func_parameter TYPE RS38L_PAR_,
  func_paramtype TYPE RS38L_KIND,
  func_structure TYPE RS38L_TYP,
  json_data      TYPE STRING,
  param_type     TYPE PARAMTEXT,
END OF gs_data.
DATA: gt_data LIKE TABLE OF gs_data.
"Prepare Data"
" PERFORM get_data.
"Get FM Parameter"
CALL FUNCTION 'RPY_FUNCTIONMODULE_READ_NEW'
  EXPORTING
    functionname       = lv_func_name
  TABLES
    import_parameter   = lt_imp_para
    changing_parameter = lt_chg_para
    export_parameter   = lt_exp_para
    tables_parameter   = lt_tab_para
    exception_list     = lt_exc_list
    documentation      = lt_document
    source             = lt_source.
CLEAR lt_param .
"Function Input Parameter"
LOOP AT lt_imp_para.
  PERFORM prepare_parameter_table USING lt_imp_para-parameter lt_imp_para-optional 
                                        lt_imp_para-typ 'I'
                               CHANGING lt_param .
ENDLOOP.
"Function Table Parameter"
LOOP AT lt_tab_para.
  PERFORM prepare_parameter_table USING lt_tab_para-parameter lt_tab_para-optional 
                                        lt_tab_para-dbstruct 'T'
                               CHANGING lt_param .
ENDLOOP.
"Call Function with parameter
CALL FUNCTION lv_func_name
  PARAMETER-TABLE
    it_par.
*&---------------------------------------------------------------------*
*&      Form  PREPARE_PARAMETER_TABLE
*&---------------------------------------------------------------------*
*       text
*----------------------------------------------------------------------*
*      -->P_LT_RSIMP_PARAMETER  text
*      -->P_LT_RSIMP_OPTIONAL  text
*      -->P_LT_RSIMP_TYP  text
*----------------------------------------------------------------------*
FORM prepare_parameter_table  USING parameter optional ref typ
                           CHANGING it_par TYPE abap_func_parmbind_tab.

  DATA wa_par TYPE abap_func_parmbind.
  DATA: lv_string TYPE string.
  DATA: lv_data_type TYPE string.

  DATA: l_tabname TYPE tabname,
        ls_dd40vv TYPE dd40vv.

  DATA: dyn_table TYPE REF TO data,
        dyn_wa TYPE REF TO data,
        dyn_value TYPE REF TO data.
  FIELD-SYMBOLS: <dyn_table> TYPE ANY TABLE,
                 <dyn_wa>    TYPE ANY,
                 <dyn_value> TYPE ANY,
                 <fval>      TYPE ANY.

  "Log记录的Function Module请求参数处理"
  READ TABLE gt_data INTO gs_data WITH KEY func_parameter = parameter.
  IF sy-subrc = 0.
    CLEAR wa_par.
    wa_par-name = parameter.              " Prarter Name "
    IF typ EQ 'T'.                        " Prarter Type "
      wa_par-kind = abap_func_tables.
    ELSEIF typ EQ 'I'.
      wa_par-kind = abap_func_exporting.
    ELSEIF typ EQ 'E'.
      wa_par-kind = abap_func_importing.
    ENDIF.
    " Parameter Value "
    " Paremeter:Structure OR Table "
    IF gs_data-json_data+0(1) = '[' OR gs_data-json_data+0(1) = '{'.
      CLEAR ls_dd40vv.
      SELECT SINGLE * FROM dd40vv INTO ls_dd40vv
       WHERE typename = ref
         AND ddlanguage = sy-langu.
      "判断表类型，如果是 Table Type 则使用 row type 为表结构"
      IF ls_dd40vv IS NOT INITIAL.
        l_tabname = ls_dd40vv-rowtype.
      ELSE.
        l_tabname = ref.
      ENDIF.
      "创建动态表结构"
      CREATE DATA dyn_table TYPE TABLE OF (l_tabname).
      "创建动态内表"
      ASSIGN dyn_table->* TO <dyn_table>.
      "创建动态工作区结构"
      CREATE DATA dyn_wa LIKE LINE OF <dyn_table>.
      "创建动态工作区"
      ASSIGN dyn_wa->* TO <dyn_wa>.
      <dyn_table> = gs_data-json_data.
      IF typ EQ 'T'.       "参数类型为表：整表数据传输"
          GET REFERENCE OF <dyn_table> INTO wa_par-value.
      ELSEIF typ EQ 'I'.   "参数类型为结构:传入单条数据"
        LOOP AT <dyn_table> INTO <dyn_wa>.
          GET REFERENCE OF <dyn_wa> INTO wa_par-value.
        ENDLOOP.
      ENDIF.
    ELSE. "Paremeter:单值传入"
      CLEAR lv_string .
      lv_string = gs_data-json_data.
      CONDENSE lv_string NO-GAPS.
      CREATE DATA dyn_value TYPE (ref).   "参照传入参数类型创建动态字段"
      ASSIGN dyn_value->* TO <dyn_value>.
      <dyn_value> = lv_string.
      GET REFERENCE OF <dref> INTO wa_par-value.
    ENDIF.
    INSERT wa_par INTO TABLE it_par.
    CLEAR wa_par.
  ELSE. "必输字段一定要处理，否则会报错"
    IF optional IS INITIAL.
      CLEAR wa_par.
      wa_par-name = parameter.
      IF typ EQ 'I'.
        wa_par-kind = abap_func_exporting.
        CREATE DATA dyn_value TYPE (ref).
        ASSIGN dyn_value->* TO <dyn_value>.
        CLEAR <dyn_value>.
        GET REFERENCE OF <dyn_value> INTO wa_par-value.
        INSERT wa_par INTO TABLE it_par.
        CLEAR wa_par.
      ELSEIF typ EQ 'T'.
        wa_par-kind = abap_func_tables.
        CLEAR ls_dd40vv.
        SELECT SINGLE * FROM dd40vv INTO ls_dd40vv
         WHERE typename = ref
           AND ddlanguage = sy-langu.
        "判断表类型，如果是 Table Type 则使用 row type 为表结构"
        IF ls_dd40vv IS NOT INITIAL.
          l_tabname = ls_dd40vv-rowtype.
        ELSE.
          l_tabname = ref.
        ENDIF.
        "创建动态表结构
        CREATE DATA dyn_table TYPE TABLE OF (l_tabname).
        "创建动态内表
        ASSIGN dyn_table->* TO <dyn_table>.
        REFRESH <dyn_table>.
        GET REFERENCE OF <dyn_table> INTO wa_par-value.
        INSERT wa_par INTO TABLE it_par.
        CLEAR wa_par.
      ENDIF.
    ENDIF.
  ENDIF.
ENDFORM.                    " PREPARE_PARAMETER_TABLE "
```

