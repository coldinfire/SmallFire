---
title: "ABAP 获取FM参数结果"
date: 2021-04-21
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### 获取FM的参数

```ABAP
REPORT zz_get_fm_detail.
DATA: ls_trans_callstack TYPE sys_calls,
      lt_trans_callstack TYPE sys_callst.
DATA: lv_trans_funtion_name TYPE string.
DATA: ls_trans_fupararef TYPE fupararef,
      lt_trans_fupararef TYPE TABLE OF fupararef.

CLEAR lt_trans_callstack[].
CALL FUNCTION 'SYSTEM_CALLSTACK'
  IMPORTING
*   callstack    = callstack
    et_callstack = lt_trans_callstack.
CLEAR: ls_trans_callstack.
READ TABLE lt_trans_callstack INTO ls_trans_callstack WITH KEY eventtype = 'FUNC'.
IF ls_trans_callstack IS INITIAL.
  EXIT.
ENDIF.
"获取FM的参数集合"
CLEAR lt_trans_fupararef[].
SELECT *
  INTO CORRESPONDING FIELDS OF TABLE lt_trans_fupararef
  FROM fupararef
 WHERE funcname = ls_trans_callstack-eventname
   AND paramtype <> 'X'.
"通过反射获取参数的详细信息"
PERFORM frm_get_detail.
```

### 通过反射的方式获取结构详细数据

```

LOOP AT lt_zz_trans_fupararef INTO ls_zz_trans_fupararef WHERE paramtype NE 'E'.
  IF ls_zz_trans_fupararef-paramtype = 'I' AND ls_zz_trans_fupararef-reference = 'X'."非值传递的传入值，不修改例程
    CONTINUE.
  ENDIF.

  IF ls_zz_trans_fupararef-paramtype NE 'T'.
    ASSIGN (ls_zz_trans_fupararef-parameter) TO <fs_zz_trans_workarea>.
    IF <fs_zz_trans_workarea> IS ASSIGNED.
      IF <fs_zz_trans_workarea> IS NOT INITIAL.
        "反射
        lo_cl_abap_typedescr = cl_abap_typedescr=>describe_by_data( <fs_zz_trans_workarea> ).
        CASE lo_cl_abap_typedescr->kind.
          WHEN 'E'. "元素
            lo_cl_abap_elemdescr ?= lo_cl_abap_typedescr.
            IF lo_cl_abap_elemdescr->edit_mask IS NOT INITIAL. "存在转换例程
              CONCATENATE 'CONVERSION_EXIT_' lo_cl_abap_elemdescr->edit_mask+2 '_INPUT' INTO lv_zz_trans_funtion_name.
*            lv_zz_trans_funtion_name = 'CONVERSION_EXIT_' && lo_cl_abap_elemdescr->edit_mask+2 && '_INPUT'.
              TRY .
                  CALL FUNCTION lv_zz_trans_funtion_name
                    EXPORTING
                      input         = <fs_zz_trans_workarea>
                    IMPORTING
                      output        = <fs_zz_trans_workarea>
                    EXCEPTIONS
                      error_message = 99.
                  IF sy-subrc <> 0.

                  ENDIF.
                CATCH cx_root INTO lo_zz_trans_root_error.
              ENDTRY.
            ENDIF.
          WHEN 'S'."结构
            "反射
            lo_cl_abap_structdescr ?= cl_abap_typedescr=>describe_by_data( <fs_zz_trans_workarea> ).
            lt_zz_trans_comp_tab[] = lo_cl_abap_structdescr->get_components( )."组成结构体的各个字段组件
            LOOP AT lt_zz_trans_comp_tab INTO ls_zz_trans_comp_descr WHERE as_include = 'X'. "递归INCLUDE结构
              REFRESH e_components_result.
              CLEAR: e_components_result.
              lo_cl_abap_structdescr ?= ls_zz_trans_comp_descr-type.
              e_components_result = lo_cl_abap_structdescr->get_components( ).
              APPEND LINES OF e_components_result[] TO lt_zz_trans_comp_tab[].
            ENDLOOP.
            LOOP AT lt_zz_trans_comp_tab INTO ls_zz_trans_comp_descr.
              IF ls_zz_trans_comp_descr-as_include = 'X'.
                DELETE lt_zz_trans_comp_tab.
                CONTINUE.
              ENDIF.
              lo_cl_abap_elemdescr ?= ls_zz_trans_comp_descr-type.
              IF lo_cl_abap_elemdescr->edit_mask IS INITIAL. "不存在转换例程
                DELETE lt_zz_trans_comp_tab.
                CONTINUE.
              ENDIF.
              "存在例程
              ASSIGN COMPONENT ls_zz_trans_comp_descr-name OF STRUCTURE <fs_zz_trans_workarea> TO <fs_zz_trans_field>.
              IF <fs_zz_trans_field> IS ASSIGNED.
                CONCATENATE 'CONVERSION_EXIT_' lo_cl_abap_elemdescr->edit_mask+2 '_INPUT' INTO lv_zz_trans_funtion_name.
*              lv_zz_trans_funtion_name = 'CONVERSION_EXIT_' && lo_cl_abap_elemdescr->edit_mask+2 && '_INPUT'.
                TRY .
                    CALL FUNCTION lv_zz_trans_funtion_name
                      EXPORTING
                        input         = <fs_zz_trans_field>
                      IMPORTING
                        output        = <fs_zz_trans_field>
                      EXCEPTIONS
                        error_message = 99.
                    IF sy-subrc <> 0.
                    ENDIF.
                  CATCH cx_root INTO lo_zz_trans_root_error.
                ENDTRY.
              ENDIF.
            ENDLOOP.
          WHEN OTHERS.
        ENDCASE.

      ENDIF.
    ENDIF.
  ELSE.
    CONCATENATE ls_zz_trans_fupararef-parameter '[]' INTO lv_zz_trans_table_name.
*    lv_zz_trans_table_name = ls_zz_trans_fupararef-PARAMETER && '[]'.
    ASSIGN (lv_zz_trans_table_name) TO <fs_zz_trans_tab>.
    IF <fs_zz_trans_tab> IS ASSIGNED.
      IF <fs_zz_trans_tab> IS NOT INITIAL.
        "获取字段转换例程
        LOOP AT <fs_zz_trans_tab> ASSIGNING <fs_zz_trans_workarea>.
          EXIT.
        ENDLOOP.
        IF sy-subrc = 0.
          "反射
          lo_cl_abap_structdescr ?= cl_abap_typedescr=>describe_by_data( <fs_zz_trans_workarea> ).
          lt_zz_trans_comp_tab[] = lo_cl_abap_structdescr->get_components( )."组成结构体的各个字段组件
          LOOP AT lt_zz_trans_comp_tab INTO ls_zz_trans_comp_descr WHERE as_include = 'X'. "递归INCLUDE结构
            CLEAR: e_components_result.
            lo_cl_abap_structdescr ?= ls_zz_trans_comp_descr-type.
            e_components_result = lo_cl_abap_structdescr->get_components( ).
            APPEND LINES OF e_components_result TO lt_zz_trans_comp_tab[].
          ENDLOOP.
          LOOP AT lt_zz_trans_comp_tab INTO ls_zz_trans_comp_descr.
            IF ls_zz_trans_comp_descr-as_include = 'X'.
              DELETE lt_zz_trans_comp_tab.
              CONTINUE.
            ENDIF.
            lo_cl_abap_elemdescr ?= ls_zz_trans_comp_descr-type.
            IF lo_cl_abap_elemdescr->edit_mask IS INITIAL. "不存在转换例程
              DELETE lt_zz_trans_comp_tab.
              CONTINUE.
            ENDIF.
          ENDLOOP.
        ENDIF.
        "转换处理
        LOOP AT <fs_zz_trans_tab> ASSIGNING <fs_zz_trans_workarea>.
          LOOP AT lt_zz_trans_comp_tab INTO ls_zz_trans_comp_descr.
            lo_cl_abap_elemdescr ?= ls_zz_trans_comp_descr-type.
            ASSIGN COMPONENT ls_zz_trans_comp_descr-name OF STRUCTURE <fs_zz_trans_workarea> TO <fs_zz_trans_field>.
            IF <fs_zz_trans_field> IS ASSIGNED.
              CONCATENATE 'CONVERSION_EXIT_' lo_cl_abap_elemdescr->edit_mask+2 '_INPUT' INTO  lv_zz_trans_funtion_name.
*              lv_zz_trans_funtion_name = 'CONVERSION_EXIT_' && lo_cl_abap_elemdescr->edit_mask+2 && '_INPUT'.
              TRY.
                  CALL FUNCTION lv_zz_trans_funtion_name
                    EXPORTING
                      input         = <fs_zz_trans_field>
                    IMPORTING
                      output        = <fs_zz_trans_field>
                    EXCEPTIONS
                      error_message = 99.
                  IF sy-subrc <> 0.
                  ENDIF.
                CATCH cx_root INTO lo_zz_trans_root_error.
              ENDTRY.
            ENDIF.
          ENDLOOP.
        ENDLOOP.
      ENDIF.
    ENDIF.
  ENDIF.
ENDLOOP.
```

