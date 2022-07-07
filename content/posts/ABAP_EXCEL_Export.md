---
title: " 内表导出到 Excel "
date: 2019-08-22
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - ALV

---

SAP 提供了 cl_salv_export_tool 类用于将 ALV 数据导出到 Excel，如果是早一点的版本，系统没有这个函数可以使用 XXL_SIMPLE_API。

### XXL_SIMPLE_API 函数

XXL_SIMPLE_API 实现将 internal table 的数据导出到 Excel，相对而言，比 OLE 和 DOI 技术更加简单。但每次导出时，弹出对话框，略显不便。

为了使用这个函数，需要先定义 3 个内表，最重要的是根据 gxxlt_v 结构创建内表，代表要在 Excel 显示的数据的表头 (heading)。这个结构和导出数据的 internal table 的字段数必须相同。

```ABAP
"GXXLT_V: Headings for DATA columns"
DATA BEGIN OF gt_gxxlt_v OCCURS 1.
  INCLUDE STRUCTURE gxxlt_v.
DATA END OF gt_gxxlt_v.
"GXXLT_O: Table with online text"
DATA BEGIN OF gt_gxxlt_o OCCURS 1.
  INCLUDE STRUCTURE gxxlt_o.
DATA END OF gt_gxxlt_v.
"GXXLT_P: Table with print texts"
DATA BEGIN OF gt_gxxlt_p OCCURS 1.
  INCLUDE STRUCTURE gxxlt_p.
DATA END OF gt_gxxlt_v.
"定义内表"
START-OF-SELECTION.
  "获取数据填充内表"
  "输出内表数据到EXCEL"
  CALL FUNCTION 'XXL_SIMPLE_API'
    EXPORTING
      filename                = 'ULO'
      header                  = ' '
      n_key_cols              = 2
     TABLES
       col_text               = gt_gxxlt_v[]
       data                   = gt_alv[]
       online_text            = gt_gxxlt_o[]
       print_text             = gt_gxxlt_p[]
    exceptions
      dim_mismatch_data       = 1
      file_open_error         = 2
      file_write_error        = 3
      inv_winsys              = 4
      inv_xxl                 = 5
      OTHERS                  = 6.
  IF sy-subrc <> 0.
    MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
       WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
  ENDIF.
      
```

### ZITAB_TO_EXCEL

IMPORT：INTERNAL_TAB TYPE ANY TABLE.

```ABAP
FUNCTION ZITAB_TO_EXCEL.
  DATA dref TYPE REF TO data.
  FIELD-SYMBOLS <itab> TYPE STANDARD TABLE.
  DATA: tabledescr_ref TYPE REF TO cl_abap_tabledescr,
        descr_ref      TYPE REF TO cl_abap_structdescr,
        comp_descr     TYPE        abap_compdescr.
  DATA: t_heading TYPE TABLE OF gxxlt_v WITH HEADER LINE,
        t_online  TYPE TABLE OF gxxlt_o,
        t_print   TYPE TABLE OF gxxlt_p.

  CREATE DATA dref TYPE STANDARD TABLE OF (INTERNAL_TAB).
  ASSIGN dref->* TO <itab>.
  
  tabledescr_ref  ?= cl_abap_structdescr=>describe_by_data( <itab> ).
  descr_ref ?= tabledescr_ref->get_table_line_type( ).
  LOOP AT descr_ref->components INTO comp_descr.
    t_heading-col_no = sy-tabix.
    t_heading-col_name = comp_descr-name.
    APPEND t_heading.
  ENDLOOP.
  
  CALL FUNCTION 'XXL_SIMPLE_API'
     TABLES
       col_text               = t_heading 
       data                   = <itab>
       online_text            = t_online
       print_text             = t_print
    exceptions
      dim_mismatch_data       = 1
      file_open_error         = 2
      file_write_error        = 3
      inv_winsys              = 4
      inv_xxl                 = 5
      OTHERS                  = 6.
ENDFUNCTION.
```

