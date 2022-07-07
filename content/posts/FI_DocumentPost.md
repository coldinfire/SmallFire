---
title: " FI 会计凭证过账 "
date: 2020-08-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO
  - DEMO

---

### BAPI_ACC_DOCUMENT_POST

该过账 BAPI 需要和模拟过账的 BAPI `BAPI_ACC_DOCUMENT_CHECK` 搭配使用，这样先执行模拟过账，成功了再执行真正的过账，这样就不会因为执行失败产生废弃的凭证，还要再进行冲销，造成底表数据冗余无用。

#### 模拟过账：BAPI_ACC_DOCUMENT_CHECK

```ABAP
DATA:
  LS_DOCUMENTHEADER  LIKE BAPIACHE09,
  LS_ACCOUNTGL       LIKE BAPIACGL09,
  LS_ACCOUNTRECEIVABLE LIKE BAPIACAR09,
  LS_ZFIS002         TYPE ZFIS0002,
  LS_CURRENCYAMOUNT  TYPE BAPIACCR09,
  LS_EXTENSION1      TYPE BAPIACEXTC,
  LS_EXTENSION2      TYPE BAPIPAREX,
  LS_RETURN          TYPE BAPIRET2,
  LT_ACCOUNTGL       TYPE TABLE OF BAPIACGL09 WITH HEADER LINE,
  LT_ACCOUNTRECEIVABLE LIKE TABLE OF BAPIACAR09 WITH HEADER LINE,
  LT_CURRENCYAMOUNT  LIKE TABLE OF BAPIACCR09 WITH HEADER LINE,
  LT_RETURN          LIKE TABLE OF BAPIRET2   WITH HEADER LINE,
  LT_PAYABLE         LIKE TABLE OF BAPIACAP09 WITH HEADER LINE,
  LT_EXTENSION1      LIKE TABLE OF BAPIACEXTC WITH HEADER LINE,
  LT_EXTENSION2      LIKE TABLE OF BAPIPAREX  WITH HEADER LINE.
DATA: LV_TYPE LIKE LS_DOCUMENTHEADER-OBJ_TYPE,
      LV_KEY  LIKE LS_DOCUMENTHEADER-OBJ_KEY,
      LV_SYS  LIKE LS_DOCUMENTHEADER-OBJ_SYS.
DATA: G_SIMU_ERROR TYPE FLAG,
      G_POST_ERROR TYPE FLAG.
CALL FUNCTION 'BAPI_ACC_DOCUMENT_CHECK'
  exporting
    documentheader    = LS_DOCUMENTHEADER
  tables
    accountgl         = LT_ACCOUNTGL
    accountreceivable = LT_ACCOUNTRECEIVABLE
    accountpayable    = LT_PAYABLE
    currencyamount    = LT_CURRENCYAMOUNT
    extension1        = LT_EXTENSION1
    extension2        = LT_EXTENSION2
    return            = LT_RETURN.
LOOP AT it_return.
  IF it_return-type eq 'E'.
    g_simu_error = 'X'.
  ENDIF.
ENDLOOP.
IF g_simu_error eq 'X'.
  WRITE: / 'I 凭证生成错误，实际未生成任何会计凭证'.
ELSE.
* 当模拟过账成功，就可以执行真正的BAPI过账:BAPI_ACC_DOCUMENT_POST
ENDIF.
```

#### 执行过账：BAPI_ACC_DOCUMENT_POST

```ABAP
call function 'BAPI_ACC_DOCUMENT_POST'
  exporting
    documentheader     = LS_DOCUMENTHEADER
*   CUSTOMERCPD        =
*   CONTRACTHEADER     =
  importing
    obj_type           = LV_TYPE   
    obj_key            = LV_KEY
    obj_sys            = LV_SYS
  tables
    accountgl          = LT_ACCOUNTGL
    accountreceivable  = LT_ACCOUNTRECEIVABLE
    accountpayable     = LT_PAYABLE
    currencyamount     = LT_CURRENCYAMOUNT
    extension1         = LT_EXTENSION1
    extension2         = LT_EXTENSION2
    return             = LT_RETURN.
loop at lt_return.
    if lt_return-type = 'E'.
      g_post_error = 'X'.
    endif.
endloop.
if g_post_error eq 'X'.
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
else.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      WAIT = 'X'.
endif.
if lv_key is not initial.
  if p_flag eq 'X'.
    clear: p_zsfkdh-d_gjahr, p_zsfkdh-d_belnr.
    move f_obj_key(10) to p_zsfkdh-d_belnr.
    move f_obj_key+14(4) to p_zsfkdh-d_gjahr.
  else.
    clear: p_zsfkdh-d_gjahr, p_zsfkdh-zkf_belnr.
    move f_obj_key(10) to p_zsfkdh-zkf_belnr.
    move f_obj_key+14(4) to p_zsfkdh-d_gjahr.
  endif.
endif.
```



