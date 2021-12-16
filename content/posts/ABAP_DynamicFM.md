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

### 获取 Function Module 参数信息



```ABAP
DATA: lv_func_name TYPE RS38L_FNAM.
DATA: lt_rsimp TYPE TABLE OF rsimp WITH HEADER LINE,
      lt_rscha TYPE TABLE OF rscha WITH HEADER LINE,
      lt_rsexp TYPE TABLE OF rsexp WITH HEADER LINE,
      lt_rstbl TYPE TABLE OF rstbl WITH HEADER LINE,
      lt_rsexc TYPE TABLE OF rsexc WITH HEADER LINE,
      lt_rsfdo TYPE TABLE OF rsfdo WITH HEADER LINE,
      lt_source TYPE TABLE OF rssource WITH HEADER LINE.
CLEAR:lt_rsimp,lt_rscha,lt_rsexp,lt_rstbl,lt_rsexc,lt_rsfdo,lt_source.
REFRESH:lt_rsimp,lt_rscha,lt_rsexp,lt_rstbl,lt_rsexc,lt_rsfdo,lt_source.
CALL FUNCTION 'RPY_FUNCTIONMODULE_READ_NEW'
  EXPORTING
    functionname       = lv_func_name
  TABLES
    import_parameter   = lt_rsimp
    changing_parameter = lt_rscha
    export_parameter   = lt_rsexp
    tables_parameter   = lt_rstbl
    exception_list     = lt_rsexc
    documentation      = lt_rsfdo
    source             = lt_source.
```

#### 函数功能

![FUNCTION MODULE](/images/ABAP/FM_Detail.png)



### 动态函数调用



```ABAP
DATA: it_par TYPE abap_func_parmbind_tab.
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

