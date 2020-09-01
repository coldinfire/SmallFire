---
title: " SAP FI 会计凭证过账 "
date: 2020-08-02
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - FICO

---

### BAPI_ACC_DOCUMENT_POST

该过账 BAPI 需要和模拟过账的 BAPI `BAPI_ACC_DOCUMENT_CHECK` 搭配使用，这样先执行模拟过账，成功了再执行真正的过账，这样就不会因为执行失败产生废弃的凭证，还要再进行冲销，造成底表数据冗余无用。

#### 模拟过账：BAPI_ACC_DOCUMENT_CHECK

```JS
CALL FUNCTION 'BAPI_ACC_DOCUMENT_CHECK'
  exporting
    documentheader    = i_doc_head
  tables
    accountgl         = it_gl[]
    accountreceivable = it_receive[]
    accountpayable    = it_pay[]
    currencyamount    = it_curramount[]
    extension1        = it_extension[]
    return            = it_return[].
LOOP AT it_return.
  IF it_return-type eq 'E'.
    g_simu_error = 'X'.
    WRITE: / it_return-type, it_return-message(100).
    set pf-status '1000'.
    LEAVE TO LIST-PROCESSING.
  ENDIF.
ENDLOOP.
IF g_simu_error eq 'X'.
  WRITE: / 'I 凭证生成错误，实际未生成任何会计凭证'.
  CLEAR: p_zsfkdh-d_belnr, p_zsfkdh-d_gjahr,p_zsfkdh-zkf_belnr.
  LEAVE SCREEN.
ENDIF.
* 当模拟过账成功，就可以执行真正的BAPI过账:BAPI_ACC_DOCUMENT_POST
```

#### 执行过账：BAPI_ACC_DOCUMENT_POST

```JS
call function 'BAPI_ACC_DOCUMENT_POST'
  exporting
    documentheader          = i_doc_head
*   CUSTOMERCPD             =
*   CONTRACTHEADER          =
  importing
    obj_type = f_obj_type   
    obj_key = f_obj_key
    obj_sys = f_obj_sys
  tables
    accountgl               = it_gl[]
    accountreceivable       = it_receive[]
    accountpayable          = it_pay[]
    currencyamount          = it_curramount[]
    extension1              = it_extension[]
    return                  = it_return[].
loop at it_return.
    if it_return-type = 'E'.
      g_post_error = 'X'.
    endif.
    write: / it_return-type, it_return-message(100).
    set pf-status '1000'.
    leave to list-processing.
endloop.
if g_post_error eq 'X'.
  clear: p_zsfkdh-d_belnr, p_zsfkdh-d_gjahr,p_zsfkdh-zkf_belnr.
  leave screen.
endif.
if f_obj_key is not initial.
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

### 完整示例程序

```JS
DATA:
  LS_DOCUMENTHEADER  LIKE BAPIACHE09,
  LS_BAPIACGL09      LIKE BAPIACGL09,
  LS_CUSTOMERCPD     LIKE BAPIACAR09,
  LS_ZFIS002         TYPE ZFIS0002,
  LS_CURRENCYAMOUNT  TYPE BAPIACCR09,
  LS_EXTENSION2      TYPE  BAPIPAREX,
  LS_RETURN          TYPE BAPIRET2,
  LT_ACCOUNTGL       TYPE TABLE OF BAPIACGL09 WITH HEADER LINE,
  LT_ACCOUNTRECEIVABLE LIKE TABLE OF BAPIACAR09 WITH HEADER LINE,
  LT_CURRENCYAMOUNT  LIKE TABLE OF BAPIACCR09 WITH HEADER LINE,
  LT_RETURN          LIKE TABLE OF BAPIRET2   WITH HEADER LINE, 
  LT_EXTENSION2      LIKE TABLE OF BAPIPAREX  WITH HEADER LINE.
DATA: LV_TYPE LIKE LS_DOCUMENTHEADER-OBJ_TYPE,
      LV_KEY  LIKE LS_DOCUMENTHEADER-OBJ_KEY,
      LV_SYS  LIKE LS_DOCUMENTHEADER-OBJ_SYS.
      
LS_DOCUMENTHEADER-HEADER_TXT = BKTXT.
LS_DOCUMENTHEADER-COMP_CODE  = BUKRS.
LS_DOCUMENTHEADER-DOC_DATE   = BUDAT.
LS_DOCUMENTHEADER-DOC_TYPE   = BLART.
LS_DOCUMENTHEADER-PSTNG_DATE = BUDAT.
LS_DOCUMENTHEADER-USERNAME   = SY-UNAME.
LS_DOCUMENTHEADER-FISC_YEAR  = SY-BUDAT+0(4).
LS_DOCUMENTHEADER-FIS_PERIOD = SY-BUDAT+4(2).
*LS_DOCUMENTHEADER-BUS_ACT    = 'RFBU'.         "业务事务
```

