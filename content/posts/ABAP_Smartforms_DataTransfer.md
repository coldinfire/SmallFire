---
title: " Smartforms 数据传输 "
date: 2018-07-24
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - smartforms

---

### 数据传输到 Smartforms

Smartforms 是一个独立的外部 Function Module，对于 ABAP 程序内部定义的内表数据不能直接传递，需要定义外部的数据结构 Structure 或者使用标准的表结构。如果 ABAP 程序变更，需要传递的数据发生变化，那么对应的 Sturcture 也需要修改，这是 Smartforms 中不方便的地方。

我们也可以在 Smartforms 内部写取数据的逻辑，但是在 Smart Forms 中编程不是很方便，而且有时我们的数据需要首先以 ALV 的方式显示，然后再打印，所以在 Smart Forms 中书写取数据逻辑只能对一些要求非常简单的场合适用。

### Method1：通过 Form Interface 接收表数据

在 `Global Settings -> Form Interface -> table ` 中定义和 ABAP 程序中数据结构相同的表参数。

### Method2：通过 Export / Import 传输数据

在 ABAP 程序中进行取数逻辑，然后将数据传递到 Smartforms  中。首先需要 Export 内表到内存或者数据库中，然后将句柄传递到 Smartforms 中，在 Smartforms 中需要定义和要显示数据结构完全相同类型的内表，最后将数据 Impor 到内表中即可完全恢复数据，这样就完成的数据的传递工作。

```ABAP
REPORT ZPRINT_SMARTFORM_DEMO.
INCLUDE ZINC_SF_HELPER.

DATA: wf_name TYPE rs38l_fnam.
DATA: lv_stp TYPE timestampl,
      lv_stp2(22).
DATA: lv_buffid1(18), "header"
      lv_buffid2(18). "detail"
" 在句柄中加上服务器当前时间作为句柄名称，防止多人同时使用该程序，导致句柄名称相同 "
GET TIME STAMP FIELD lv_stp.
lv_stp2 = lv_stp.
CONCATENATE 'HEADER' lv_stp2+8(14) INTO lv_buffid1.
CONCATENATE 'DETAIL' lv_stp2+8(14) INTO lv_buffid2.
"保存数据到内存中"
savebuffer it_header[] lv_buffid1. "保存输出表单表头数据的内表"
savebuffer it_detail[] lv_buffid2. "保存输出数据明细的内表"
"调用 Smartforms"
CALL FUNCTION 'SSF_FUNCTION_MODULE_NAME'
  EXPORTING
    formname           = 'Form_name'
  IMPORTING
    fm_name            = wf_name
  EXCEPTIONS
    no_form            = 1
    no_function_module = 2
    OTHERS             = 3.
IF sy-subrc <> 0.
  MESSAGE ID sy-msgid TYPE sy-msgty NUMBER sy-msgno
    WITH sy-msgv1 sy-msgv2 sy-msgv3 sy-msgv4.
ENDIF.
CALL FUNCTION wf_name
  EXPORTING
    user_settings      = ''
    id_header          = vl_buffid1
    id_detail          = vl_buffid2
    control_parameters = control
    output_options     = options
  EXCEPTIONS
    formatting_error   = 1
    internal_error     = 2
    send_error         = 3
    user_canceled      = 4
    OTHERS             = 5.
IF sy-subrc <> 0.
  MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
    WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
"调用完毕以后，删除数据"
clearbuffer lv_buffid1.  
clearbuffer lv_buffid2.
```

#### Include 程序

```ABAP
*&---------------------------------------------------------------------*
*&  包括              ZINC_SF_HELPER                                   *
*&---------------------------------------------------------------------*
TYPES buffer_id(80) TYPE c.
DATA wa_indx TYPE indx.
DEFINE savebuffer.
  perform save_to_buffer using &1 &2.
END-OF-DEFINITION.
DEFINE clearbuffer.
  perform clear_buffer using &1.
END-OF-DEFINITION.
*&--------------------------------------------------------------------*
*&      Form  Save_To_Buffer
*&--------------------------------------------------------------------*
*       text
*---------------------------------------------------------------------*
*      -->T          text
*      -->BUFF_ID    text
*---------------------------------------------------------------------*
FORM save_to_buffer USING t_data TYPE table typeid TYPE c .
  wa_indx-aedat = sy-datum.
  wa_indx-usera = sy-uname.
  wa_indx-pgmid = sy-repid.
  EXPORT t_data TO DATABASE indx(hk) ID typeid from wa_indx.
ENDFORM.                    "Save_To_Buffer"
*&--------------------------------------------------------------------*
*&      Form  Clear_Buffer
*&--------------------------------------------------------------------*
*       text
*---------------------------------------------------------------------*
*      -->BUFF_ID    text
*---------------------------------------------------------------------*
FORM clear_buffer USING buffid TYPE c.
  DELETE FROM DATABASE indx(hk) ID buffid.
ENDFORM.                    "Clear_Buffer"
```

#### Smartform 接收数据

在 Smartform 中获取程序传入的表头和明细的数据。

##### Global Settings  ->  Form Interface  ->  Import 设置参数

定义两个参数用来传入我们在 Report 中 Export 内表数据的句柄 (ID key)。

- `i_header type c`
- `i_items type c`

##### Global Settings  ->  Global definitions

*Types 定义*

在这里需要定义4个类型，一个用来保存表头数据的工作区和内表，一个用来保存明细数据的工作区和内表，它们的结构必须与 Report 中 Export 到数据库中的内表的结构完全对应一致。

```ABAP
* 抬头信息
TYPES:BEGIN OF TYP_header_ROW ,
        mblnr          LIKE mseg-mblnr,    
        bldat          LIKE rkpf-rsdat,   
        bwart          LIKE mseg-bwart,   
END OF TYP_header_ROW .
TYPES: TYP_HEADER_TABLE TYPE TABLE OF TYP_HEADER_ROW WITH HEADER LINE
* 明细信息
TYPES:BEGIN OF TYP_ITEMS_ROW ,
        mblnr       LIKE mseg-mblnr,     
        rsnum       LIKE rkpf-rsnum,     
        mjahr       LIKE mseg-mjahr,     
        zeile       LIKE mseg-zeile,     
        werks       LIKE mseg-werks,      
        matnr       LIKE mseg-matnr,      
        maktx       LIKE makt-maktx,      
END OF TYP_ITEMS_ROW.
TYPES: TYP_ITEMS_TABLE TYPE TABLE OF TYP_ITEMS_ROW WITH HEADER LINE.
```

*Global Data 定义*

定义工作区，用于循环数据的处理。

```ABAP
gt_header type typ_header_table
wa_header type typ_header_row
gt_items  type typ_items_table
wa_items  type typ_items_row
gt_blanks type typ_items_table
wa_blanks type typ_items_row
g_totallines type i
```

*Initialization 数据*

```ABAP
PERFORM restor_buffer USING i_header CHANGING gt_header.
PERFORM restor_buffer USING i_items  CHANGING gt_items.
DESCRIBE TABLE gt_items LINES g_totallines.
```

*Form Roution 定义函数*

```ABAP
FORM restor_buffer USING typeid TYPE c CHANGING t_data TYPE table.
  IMPORT t_data FROM DATABASE indx(hk) ID typeid.
ENDFORM.
```

