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

- 

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
* Dynamic FM call with all import,export,table parameters in the Parameter-table
CALL FUNCTION fm_name
  PARAMETER–TABLE lt_param.
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

#### 函数功能

![FUNCTION MODULE](/images/ABAP/FM_Detail.png)

#### 调用函数

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

#### 动态实例

```ABAP
REPORT zdyn_call_fm.
TYPE-POOLS:abap,slis.
DATA: lv_func_name TYPE rs38l_fnam.
DATA: lv_msg TYPE string.
DATA: it_param TYPE abap_func_parmbind_tab,
      wa_param TYPE abap_func_parmbind,
      it_excep TYPE abap_func_excpbind_tab,
      wa_excep TYPE abap_func_excpbind.
FIELD-SYMBOLS: <fs_msg> TYPE string.

CLEAR it_par.
"Function Input Parameter"
LOOP AT lt_rsimp.
  PERFORM prepare_parameter_table USING lt_rsimp-parameter lt_rsimp-optional 
                                        lt_rsimp-typ 'I'
                               CHANGING it_par.
ENDLOOP.
"Function Table Parameter"
LOOP AT lt_rstbl.
  PERFORM prepare_parameter_table USING lt_rstbl-parameter lt_rstbl-optional 
                                        lt_rstbl-dbstruct 'T'
                               CHANGING it_par.
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
  DATA: lv_json_string TYPE string.
  DATA: lv_data_type TYPE string.

  DATA: l_tabname TYPE tabname,
        ls_dd40vv TYPE dd40vv.

  DATA: dyn_table TYPE REF TO data,
        dyn_wa TYPE REF TO data,
        dyn_value TYPE REF TO data.
  FIELD-SYMBOLS: <dyn_table> TYPE ANY TABLE,
                 <dyn_wa>    TYPE ANY,
                 <dref>      TYPE ANY,
                 <fval>      TYPE ANY.

  DATA: lv_1 TYPE string,
        lv_2 TYPE string,
        lv_3 TYPE string,
        lv_spanid TYPE string,
        lv_name TYPE adrp-name_text.

  "Log记录的Function Module请求参数处理"
  READ TABLE gt_data_rst INTO gs_data WITH KEY func_parameter = parameter.
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
      IF gs_data-json_data+0(1) = '['.
        lv_json_string = gs_data-json_data.
      ELSEIF gs_data-json_data+0(1) = '{'.
        CONCATENATE '[' gs_data-json_data ']' INTO lv_json_string.
      ENDIF.
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
      zui2_json=>deserialize( EXPORTING json = lv_json_string
                                        pretty_name = zui2_json=>pretty_mode-camel_case
                              CHANGING data = <dyn_table> ).
      IF typ EQ 'T'. "如果是传入参数则取传入的值，非传入参数置空"
        IF gs_data-param_type EQ 'IMPORT'.
          GET REFERENCE OF <dyn_table> INTO wa_par-value.
        ELSE.
          REFRESH <dyn_table>.
          GET REFERENCE OF <dyn_table> INTO wa_par-value.
        ENDIF.
      ELSEIF typ EQ 'I'."传入类型为结构:传入单条数据"
        LOOP AT <dyn_table> INTO <dyn_wa>.
          "更新Frame传入参数"
          IF parameter EQ 'I_FRAME_HEAD'.
            ASSIGN COMPONENT 'SPANID' OF STRUCTURE <dyn_wa> TO <fval>.
            SPLIT <fval> AT '.' INTO lv_1 lv_2 lv_3 .
            lv_spanid = lv_3 + 1.
            CONCATENATE lv_1 '.' lv_2 '.' lv_spanid INTO <fval>.
            CONDENSE <fval> NO-GAPS.
            ASSIGN COMPONENT 'CDATE' OF STRUCTURE <dyn_wa> TO <fval>.
            <fval> = sy-datum.
            ASSIGN COMPONENT 'CTIME' OF STRUCTURE <dyn_wa> TO <fval>.
            <fval> = sy-uzeit.
            ASSIGN COMPONENT 'USERID' OF STRUCTURE <dyn_wa> TO <fval>.
            <fval> = sy-uname.
            ASSIGN COMPONENT 'USERNAME' OF STRUCTURE <dyn_wa> TO <fval>.
            CLEAR lv_name.
            SELECT SINGLE name_text INTO lv_name
              FROM usr21 INNER JOIN adrp ON usr21~persnumber = adrp~persnumber
              WHERE bname = sy-uname.
            <fval> = lv_name.
          ENDIF.
          GET REFERENCE OF <dyn_wa> INTO wa_par-value.
        ENDLOOP.
      ENDIF.
    ELSE. "Paremeter:单值传入"
      CLEAR lv_json_string .
      lv_json_string = gs_data-json_data.
      SHIFT lv_json_string LEFT DELETING LEADING '"'.
      SHIFT lv_json_string RIGHT DELETING TRAILING '"'.
      CONDENSE lv_json_string NO-GAPS.
      CREATE DATA dyn_value TYPE (ref).   "参照传入参数类型创建动态字段"
      ASSIGN dyn_value->* TO <dref>.
      <dref> = lv_json_string.
      GET REFERENCE OF <dref> INTO wa_par-value.
    ENDIF.

    INSERT wa_par INTO TABLE it_par.
    CLEAR wa_par.
  ELSE.
    IF optional IS INITIAL.
      CLEAR wa_par.
      wa_par-name = parameter.
      IF typ EQ 'I'.
        wa_par-kind = abap_func_exporting.
        CREATE DATA dyn_value TYPE (ref).
        ASSIGN dyn_value->* TO <dref>.
        CLEAR <dref>.
        GET REFERENCE OF <dref> INTO wa_par-value.
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

