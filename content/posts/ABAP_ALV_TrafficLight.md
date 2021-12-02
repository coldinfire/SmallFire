---
title: " ALV 显示红绿灯 "
date: 2018-06-14
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - abaputils
  - ALV

---

### 定义 ###
```ABAP
TYPE-POOLS:icon,sym,slis,col.
DATA: l_alv_filed     TYPE slis_fieldcat_alv,
      l_alv_filedcat  TYPE slis_t_fieldcat_alv.
TYPES:BEGIN OF str_alv,
      id  TYPE char25,
      xxx TYPE xxx,
      xxx TYPE xxx,
      xxx TYPE xxx,
      xxx TYPE string,
END OF str_alv.
DATA:it_alv TYPE STANDARD TABLE OF str_alv WITH HEADER LINE.
CONSTANTS: c_green  TYPE icon-id VALUE '@08@',
           c_yellow TYPE icon-id VALUE '@09@',
           c_red    TYPE icon-id VALUE '@0A@'.
```
### 赋值 ###
```ABAP
IF it_return-type = 'E'.
   it_alv-id = c_red.
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
ELSE.
   it_alv-id = c_green.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
     EXPORTING
        wait = 'X'.
ENDIF.
it_alv-gjahr = wa_sign-gjahr.
it_alv-iblnr = wa_sign-iblnr.
it_alv-zeili = wa_sign-zeili.
it_alv-mes   = it_return-message.
APPEND it_alv.
```
### 显示 ###
```ABAP
clear l_alv_filed.
l_alv_filed-col_pos = 1.
l_alv_filed-fieldname = 'ID'.
l_alv_filed-inttype = 'C'.
l_alv_filed-seltext_m = 'Status'.
l_alv_filed-icon = 'X'.
append l_alv_filed to l_alv_filedcat.

clear l_alv_filed.
l_alv_filed-col_pos = 2.
l_alv_filed-fieldname = 'GJAHR'.
l_alv_filed-seltext_m = 'Year.'.
append l_alv_filed to l_alv_filedcat.
call function 'REUSE_ALV_GRID_DISPLAY'
  exporting
    it_fieldcat  = gt_fieldcat
  tables
      t_outtab   = it_alv
      t_fieldcat = l_alv_filedcat.
```