---
title: " Grid ALV:Fieldcat 自动填充工具 "
date: 2021-06-07
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils

---

### TOP

```ABAP
TYPE-POOLS slis.
FORM frm_get_fields USING pt_data type any table
                    CHANGING pt_fields TYPE ddfields.
  DATA: lr_tabdescr TYPE REF TO cl_abap_structdescr,
        lr_data TYPE REF TO data,
        lt_fields TYPE ddfields.
  CREATE DATA lr_data LIKE LINE OF pt_data.
  lr_tabdescr ?= cl_abap_structdescr=>describe_by_data_ref( lr_data ).
  lt_fields = cl_salv_data_descr=>read_structdescr( lr_tabdescr ).
  pt_fields = lt_fields.
ENDFORM.
```

### Z_FALV_FIELD_CATALOG

IMPORT：IT_ALV TYPE ANY TABLE.

TABLES：FIELD_CATALOG TYPE SLIS_T_FIELDCAT_ALV.

```ABAP
DATA: lt_ddfields TYPE ddfields,
      ls_fields TYPE dfies.
DATA: ls_fieldcat TYPE slis_fieldcat_alv,
      lt_fieldcat TYPE slis_t_fieldcat_alv.

PERFORM frm_get_fields USING it_alv[] CHANGING lt_ddfields[].
LOOP AT lt_ddfields into ls_fields.
  MOVE-CORRESPONDING ls_fields TO ls_fieldcat.
  APPEND ls_fieldcat TO lt_fieldcat.
  CLEAR ls_fieldcat.
ENDLOOP.
APPEND LINES OF lt_fieldcat TO field_catalog.
```

