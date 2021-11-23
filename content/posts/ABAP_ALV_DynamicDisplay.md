---
title: " 动态显示 ALV "
date: 2018-07-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - ALV
 
---

### Step1：创建动态的 Fieldcat

和普通创建 fieldcat 的方法相同，不过通过循环去创建动态的列。

### Step2：创建动态的内表

根据上一步骤创建的 fieldcat 创建动态的内表，使用cl_alv_table_create=>create_dynamic_table。

### Step3：指针为动态表赋值

- `ASSIGN gt_dyntab->* TO <dyntab>.`：将字段符号指向新创建出来的内表对象
- `CREATE DATA gs_dyntab LIKE LINE OF <dyntab>.`：根据动态内表名动态创建数据
- `ASSIGN gs_dyntab->* to <fs_wa>.`：将字段符号指向新创建出来的结构对象

### Step4：显示 ALV

```ABAP
REPORT zdyn_table_test.
TABLES: vbap.
TYPES:BEGIN OF typ_vbap,
  vbeln TYPE vbap-vbeln,
  posnr TYPE vbap-posnr,
END OF typ_vbap.
DATA: gt_vbap TYPE TABLE OF typ_vbap,
      gs_vbap TYPE typ_vbap.
* Dynamic table
DATA: gt_dyntab TYPE REF TO data,
      gs_dyntab TYPE REF TO data.
FIELD-SYMBOLS:
      <dyntab> TYPE STANDARD TABLE,
      <fs_wa>  TYPE ANY,
      <fs_val> TYPE ANY.
* ALV data
DATA: gt_fcat TYPE lvc_t_fcat,
      gs_fcat TYPE lvc_s_fcat.
DATA: g_fname TYPE char2.
DEFINE fcat.
  gs_fcat-fieldname = &1.
  gs_fcat-scrtext_l = &2.
  append gs_fcat to gt_fcat.
  clear gs_fcat.
END-OF-DEFINITION.

PARAMETERS p_col TYPE i.

START-OF-SELECTION.
  SELECT vbeln posnr
    INTO TABLE gt_vbap
    FROM vbap
    UP TO p_col ROWS.
* Fieldcat
  fcat 'VBELN' 'SO'.
  fcat 'POSNR' 'SOITEM'.
  DO p_col TIMES.
    g_fname = sy-index.
    CONCATENATE 'F' g_fname INTO g_fname.
    fcat g_fname g_fname.
  ENDDO.
* Dynamic table create
  CALL METHOD cl_alv_table_create=>create_dynamic_table
    EXPORTING
      it_fieldcatalog           = gt_fcat
    IMPORTING
      ep_table                  = gt_dyntab
    EXCEPTIONS
      generate_subpool_dir_full = 1
      OTHERS                    = 2.
  IF sy-subrc <> 0.
    MESSAGE 'create dynamic table error' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
* Dynamic table data fill
  ASSIGN gt_dyntab->* TO <dyntab>.
  CREATE DATA gs_dyntab LIKE LINE OF <dyntab>.
  ASSIGN gs_dyntab->* TO <fs_wa>.
  LOOP AT gt_vbap INTO gs_vbap.
    ASSIGN COMPONENT 'VBELN' OF STRUCTURE <fs_wa> TO <fs_val>.
    <fs_val> = gs_vbap-vbeln.
    ASSIGN COMPONENT 'POSNR' OF STRUCTURE <fs_wa> TO <fs_val>.
    <fs_val> = gs_vbap-posnr.
    APPEND <fs_wa> TO <dyntab>.
  ENDLOOP.
* Alv display
  CALL FUNCTION 'REUSE_ALV_GRID_DISPLAY_LVC'
    EXPORTING
      i_callback_program = sy-repid
      it_fieldcat_lvc    = gt_fcat
    TABLES
      t_outtab           = <dyntab>
    EXCEPTIONS
      program_error      = 1
      OTHERS             = 2.
  IF sy-subrc <> 0.
    MESSAGE 'alv display error' TYPE 'S' DISPLAY LIKE 'E'.
    LEAVE LIST-PROCESSING.
  ENDIF.
```

