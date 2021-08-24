---
title: "使用BAPI创建Purchase Order"
date: 2020-06-16
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - MM

---

### 通过BAPI创建PO

```html
FUNCTION zcreate_po.
*"----------------------------------------------------------------------
*"*"Local interface:
*"  IMPORTING
*"     VALUE(CALLNO) TYPE  ZCALLNO
*"     VALUE(DOC_TYPE) LIKE  BAPIMEPOHEADER-DOC_TYPE DEFAULT 'ZIPR'
*"     VALUE(PURCH_ORG) LIKE  BAPIMEPOHEADER-PURCH_ORG DEFAULT 'POIC'
*"     VALUE(PUR_GROUP) LIKE  BAPIMEPOHEADER-PUR_GROUP
*"     VALUE(VENDOR) LIKE  BAPIMEPOHEADER-VENDOR
*"     VALUE(COMP_CODE) LIKE  BAPIMEPOHEADER-COMP_CODE DEFAULT 'GF50'
*"     VALUE(DZTERM) TYPE  DZTERM DEFAULT '0001'
*"  EXPORTING
*"     VALUE(PO_NUMBER) LIKE  BAPIMEPOHEADER-PO_NUMBER
*"     VALUE(FLAG) LIKE  BAPIRET2-TYPE
*"     VALUE(MESSAGE) LIKE  BAPIRET2-MESSAGE
*"  TABLES
*"      POITEM STRUCTURE  ZPOITEM
*"----------------------------------------------------------------------
DATA: lv_currency TYPE bapimepoheader-currency VALUE 'RMB',        "货币码"
      ls_poitem   TYPE zpoitem,
      lv_message  TYPE bapiret2-message,
      lv_po_item  TYPE bapimepoitem-po_item,                       "行项目号"
      lv_pckg_no  TYPE bapimepoitem-pckg_no.                       "PCKG_NO"
DATA: quantity(18),
      net_price(37),
      limit(27),
      exp_value(27),
      l_lifnr   TYPE lfa1-lifnr,
      lv_datano TYPE zdatano.
DATA: lt_eskl TYPE TABLE OF eskl WITH HEADER LINE.
CLEAR:gs_poheader,    gs_poheaderx,  gv_po_number,  gt_return,      gs_return,     gt_poitem,
      gs_poitem,      gt_poitemx,    gs_poitemx,    gt_poschedule,  gs_poschedule, gt_poschedulex,
      gs_poschedulex, gt_poaccount,  gs_poaccount,  gt_poaccountx,  gs_poaccountx, gt_polimits,
      gs_polimits.
*数据检查.
PERFORM frm_check_data USING doc_type purch_org pur_group vendor comp_code poitem[]
                       CHANGING flag message.
*补齐前导零
CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
  EXPORTING
    input    = vendor
 IMPORTING
   output    = l_lifnr.
*抬头数据
gs_poheader-doc_type   = doc_type.      "采购凭证类型
gs_poheader-purch_org  = purch_org.     "采购组织
gs_poheader-pur_group  = pur_group.     "采购组
gs_poheader-vendor     = l_lifnr.       "供应商帐户号
gs_poheader-comp_code  = comp_code.     "公司代码
gs_poheader-currency   = lv_currency.   "货币码
gs_poheader-pmnttrms   = dzterm.        "付款条件
"抬头数据标识"
gs_poheaderx-doc_type  = g_flag.
gs_poheaderx-purch_org = g_flag.
gs_poheaderx-pur_group = g_flag.
gs_poheaderx-vendor    = g_flag.
gs_poheaderx-comp_code = g_flag.
gs_poheaderx-currency  = g_flag.
gs_poheaderx-pmnttrms  = g_flag.
SELECT * INTO TABLE  lt_eskl FROM eskl UP TO 500 ROWS
    WHERE loekz = '' .
LOOP AT poitem INTO ls_poitem.
*行项目
   READ TABLE lt_eskl INDEX sy-tabix.
   lv_pckg_no = lt_eskl-packno.
   lv_po_item = lv_po_item + 10.
   PERFORM frm_poitem USING ls_poitem lv_po_item lv_pckg_no.
*计划行
   PERFORM frm_poschedule USING ls_poitem lv_po_item.
*账户分配
   PERFORM frm_poaccount USING ls_poitem lv_po_item.
*服务限制
   PERFORM frm_polimits USING ls_poitem lv_po_item lv_pckg_no.
ENDLOOP.
*创建采购订单
CALL FUNCTION 'BAPI_PO_CREATE1'
  EXPORTING
    poheader             = gs_poheader
    poheaderx            = gs_poheaderx
  IMPORTING
    exppurchaseorder     = gv_po_number
  TABLES
    return               = gt_return
    poitem               = gt_poitem
    poitemx              = gt_poitemx
    poschedule           = gt_poschedule
    poschedulex          = gt_poschedulex
    poaccount            = gt_poaccount
    poaccountx           = gt_poaccountx
    polimits             = gt_polimits.
  READ TABLE gt_return INTO gs_return WITH KEY type = 'E'.
  IF sy-subrc NE 0.
    po_number = gv_po_number.
    flag = 'S'.
    CONCATENATE '生成PO:' po_number INTO message.
    CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
      EXPORTING
        wait = 'X'.
  ELSE.
    CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
    LOOP AT gt_return INTO gs_return WHERE type CA 'AEX'.
      MESSAGE ID gs_return-id
            TYPE gs_return-type
          NUMBER gs_return-number
            WITH gs_return-message_v1 gs_return-message_v2
                 gs_return-message_v3 gs_return-message_v4
            INTO lv_message.
      CONCATENATE message lv_message INTO message SEPARATED BY '｜'.
    ENDLOOP.
    SHIFT message LEFT DELETING LEADING  '｜'.
    flag = 'E'.
  ENDIF.
ENDFUNCTION.
```

### 根据PR创建PO

```html
LOOP AT PRITEM.
   IF SY-TABIX = 1.
     LS_POHEADER-DOC_TYPE   = 'ZSTL'.
     CONCATENATE '000000' PRITEM-PLANT INTO LS_POHEADER-VENDOR.
     IF LS_WERKS = '1020'.
       LS_POHEADER-PURCH_ORG  = '1022'.  "pritem-purch_org"
     ELSEIF LS_WERKS = '1040'.
       LS_POHEADER-PURCH_ORG  = '1042'.
     ENDIF.
     LS_POHEADER-COMP_CODE  = '1000'.
     LS_POHEADER-PUR_GROUP  = PRITEM-PUR_GROUP.
     LS_POHEADER-DOC_DATE   = SY-DATUM.
   ENDIF.
   LT_POITEM-PO_ITEM    = PRITEM-PREQ_ITEM.
   LT_POITEM-MATERIAL   = PRITEM-MATERIAL.
   LT_POITEM-PLANT      = '1000'.
   LT_POITEM-VEND_MAT   = PRITEM-VEND_MAT.
   LT_POITEM-QUANTITY   = PRITEM-QUANTITY.
   LT_POITEM-PO_UNIT    = PRITEM-UNIT.
   IF PRITEM-CURRENCY <> 'HKD'.
   	 CLEAR:LT_TCURR,LT_TCURR[],LS_EXCHRATE.
     SELECT * FROM TCURR INTO TABLE LT_TCURR
       WHERE KURST = 'M'
       AND FCURR = PRITEM-CURRENCY
       AND TCURR = 'HKD'.
     SORT LT_TCURR BY GDATU ASCENDING.
     LOOP AT LT_TCURR.
       LT_TCURR-GDATU = '99999999' - LT_TCURR-GDATU.
       IF LT_TCURR-GDATU <= SY-DATUM.
         LS_EXCHRATE = LT_TCURR-UKURS.
         EXIT.
       ENDIF.
     ENDLOOP.
     IF LS_EXCHRATE IS NOT INITIAL.
       LT_POITEM-NET_PRICE  = PRITEM-PREQ_PRICE * LS_EXCHRATE.
     ELSE.
       LT_POITEM-NET_PRICE  = PRITEM-PREQ_PRICE.
     ENDIF.
   ELSE.
     LT_POITEM-NET_PRICE  = PRITEM-PREQ_PRICE.
   ENDIF.
   
   LT_POITEM-PRICE_UNIT = PRITEM-PRICE_UNIT.
   LT_POITEM-ACCTASSCAT = 'F'.
   APPEND LT_POITEM TO LT_POITEM.
   
   LT_POCOND-ITM_NUMBER = PRITEM-PREQ_ITEM.
   LT_POCOND-COND_TYPE  = 'PBXX'.
   LT_POCOND-COND_VALUE = LT_POITEM-NET_PRICE.
   LT_POCOND-CURRENCY   = 'HKD'.
   LT_POCOND-CHANGE_ID  = 'U'.
   APPEND LT_POCOND TO LT_POCOND.
   CLEAR:LT_POCOND,LT_POITEM.
   CLEAR:PRITEM-ACCTASSCAT.
   MODIFY PRITEM TRANSPORTING ACCTASSCAT.
ENDLOOP.

LOOP AT PRITEMX.
  IF SY-TABIX = 1.
    LS_POHEADERX-DOC_TYPE  = 'X'.
    LS_POHEADERX-VENDOR    = 'X'.
    LS_POHEADERX-PURCH_ORG = 'X'.
    LS_POHEADERX-PUR_GROUP = 'X'.
    LS_POHEADERX-DOC_DATE  = 'X'.
  ENDIF.
  LT_POITEMX-PO_ITEM    = PRITEMX-PREQ_ITEM.
  LT_POITEMX-PO_ITEMX   = 'X'.
  LT_POITEMX-MATERIAL   = 'X'.
  LT_POITEMX-PLANT      = 'X'.
  LT_POITEMX-VEND_MAT   = 'X'.
  LT_POITEMX-QUANTITY   = 'X'.
  LT_POITEMX-NET_PRICE  = 'X'.
  LT_POITEMX-PRICE_UNIT = 'X'.
  LT_POITEMX-ACCTASSCAT = 'X'.
  APPEND LT_POITEMX TO LT_POITEMX.
  CLEAR: LT_POITEMX.
  LT_POCONDX-ITM_NUMBER = PRITEMX-PREQ_ITEM.
  LT_POCONDX-COND_TYPE  = 'X'.
  LT_POCONDX-COND_VALUE = 'X'.
  LT_POCONDX-CURRENCY   = 'X'.
  LT_POCONDX-CHANGE_ID  = 'X'.
  APPEND LT_POCONDX TO LT_POCONDX.
  CLEAR:LT_POCONDX.
  CLEAR PRITEMX-ACCTASSCAT.
  MODIFY PRITEMX TRANSPORTING ACCTASSCAT.
ENDLOOP.

LOOP AT PRACCOUNT.
  LT_POACCOUNT-PO_ITEM    = PRACCOUNT-PREQ_ITEM.
  LT_POACCOUNT-SERIAL_NO  = PRACCOUNT-SERIAL_NO.
  LT_POACCOUNT-GL_ACCOUNT = PRACCOUNT-GL_ACCOUNT.
  LT_POACCOUNT-ORDERID    = PRACCOUNT-ORDERID.
  APPEND LT_POACCOUNT TO LT_POACCOUNT.
  CLEAR: LT_POACCOUNT.
ENDLOOP.

LOOP AT PRACCOUNTX.
  LT_POACCOUNTX-PO_ITEM    = PRACCOUNTX-PREQ_ITEM.
  LT_POACCOUNTX-SERIAL_NO  = PRACCOUNTX-SERIAL_NO.
  LT_POACCOUNTX-PO_ITEMX   = 'X'.
  LT_POACCOUNTX-GL_ACCOUNT = 'X'.
  LT_POACCOUNTX-ORDERID    = 'X'.
  APPEND LT_POACCOUNTX TO LT_POACCOUNTX.
  CLEAR: LT_POACCOUNTX.
ENDLOOP.
CLEAR:PRACCOUNT,PRACCOUNT[],PRACCOUNTX,PRACCOUNTX[].

LOOP AT PRHEADERTEXT.
  LT_POTEXTHEADER-PO_NUMBER  = PRHEADERTEXT-PREQ_NO.
  LT_POTEXTHEADER-PO_ITEM    = PRHEADERTEXT-PREQ_ITEM.
  LT_POTEXTHEADER-TEXT_ID    = PRHEADERTEXT-TEXT_ID.
  LT_POTEXTHEADER-TEXT_LINE  = PRHEADERTEXT-TEXT_LINE.
  APPEND LT_POTEXTHEADER TO LT_POTEXTHEADER.
  CLEAR: LT_POTEXTHEADER.
ENDLOOP.
        
CALL FUNCTION 'BAPI_PO_CREATE1'
  EXPORTING
	POHEADER               = LS_POHEADER
	POHEADERX              = LS_POHEADERX
  	POADDRVENDOR           = LS_POADDRVENDOR
   	TESTRUN                = LS_TESTRUNPO
   	MEMORY_UNCOMPLETE      = LS_MEMORY_UNCOMPLETE
    MEMORY_COMPLETE        = LS_MEMORY_COMPLETE
    POEXPIMPHEADER         = LS_POEXPIMPHEADER
    POEXPIMPHEADERX        = LS_POEXPIMPHEADERX
    VERSIONS               = LS_VERSIONS
    NO_MESSAGING           = LS_NO_MESSAGING
    NO_MESSAGE_REQ         = LS_NO_MESSAGE_REQ
    NO_AUTHORITY           = LS_NO_AUTHORITY
    NO_PRICE_FROM_PO       = LS_NO_PRICE_FROM_PO
  IMPORTING
    EXPPURCHASEORDER       = LS_EXPPURCHASEORDER
    EXPHEADER              = LS_EXPHEADER
    EXPPOEXPIMPHEADER      = LS_EXPPOEXPIMPHEADER
  TABLES
    RETURN                 = LT_RETURNPO
    POITEM                 = LT_POITEM
    POITEMX                = LT_POITEMX
    POADDRDELIVERY         = LT_POADDRDELIVERY
    POSCHEDULE             = LT_POSCHEDULE
    POSCHEDULEX            = LT_POSCHEDULEX
    POACCOUNT              = LT_POACCOUNT
    POACCOUNTPROFITSEGMENT = LT_POACCOUNTPROFITSEGMENT
    POACCOUNTX             = LT_POACCOUNTX
    POCONDHEADER           = LT_POCONDHEADER
    POCONDHEADERX          = LT_POCONDHEADERX
    POCOND                 = LT_POCOND
    POCONDX                = LT_POCONDX
    POLIMITS               = LT_POLIMITS
    POCONTRACTLIMITS       = LT_POCONTRACTLIMITS
    POSERVICES             = LT_POSERVICES
    POSRVACCESSVALUES      = LT_POSRVACCESSVALUES
    POSERVICESTEXT         = LT_POSERVICESTEXT
    EXTENSIONIN            = LT_EXTENSIONIN
    EXTENSIONOUT           = LT_EXTENSIONOUT
    POEXPIMPITEM           = LT_POEXPIMPITEM
    POEXPIMPITEMX          = LT_POEXPIMPITEMX
    POTEXTHEADER           = LT_POTEXTHEADER
    POTEXTITEM             = LT_POTEXTITEM
    ALLVERSIONS            = LT_ALLVERSIONS
    POPARTNER              = LT_POPARTNER
    POCOMPONENTS           = LT_POCOMPONENTS
    POCOMPONENTSX          = LT_POCOMPONENTSX
    POSHIPPING             = LT_POSHIPPING
    POSHIPPINGX            = LT_POSHIPPINGX
    POSHIPPINGEXP          = LT_POSHIPPINGEXP.
    
    LOOP AT pr_return WHERE type = 'E' OR type = 'A'.
    ENDLOOP.
    IF sy-subrc = 0.
      CLEAR: lv_message.
      CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
      LOOP AT pr_return INTO l_return WHERE type = 'E' .
        CONCATENATE lv_message l_return-message ';'
            INTO lv_message.
      ENDLOOP.
      CONCATENATE gt_data-bednr lv_message INTO gt_out-text.
      APPEND gt_out.
      CLEAR gt_out.
    ELSE.
      CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
        EXPORTING
          wait = 'X'.
      CONCATENATE pr_no '创建成功' INTO gt_out-text.
      APPEND gt_out.
      CLEAR gt_out.
    ENDIF.
    FREE pr_item.
    FREE pr_account.
    FREE pr_item_id.
    FREE pr_return.
  ENDLOOP.
```

### 删除PO

```html
FUNCTION zchange_po.
*"----------------------------------------------------------------------
*"*"Local interface:
*"  IMPORTING
*"     VALUE(CALLNO) TYPE  ZCALLNO
*"     VALUE(PO_NUMBER) LIKE  BAPIMEPOHEADER-PO_NUMBER
*"  EXPORTING
*"     VALUE(FLAG) LIKE  BAPIRET2-TYPE
*"     VALUE(MESSAGE) LIKE  BAPIRET2-MESSAGE
*"  TABLES
*"      ZPOITEM STRUCTURE  ZPOITEM01
*"----------------------------------------------------------------------
DATA: ls_zpoitem TYPE zpoitem01,
      lv_message TYPE bapiret2-message,
      lv_datano  TYPE zdatano.
DATA: gv_po_number LIKE  bapimepoheader-po_number,              
      gt_return    LIKE  TABLE OF bapiret2,                
      gs_return    LIKE  LINE  OF gt_return,
      gt_poitem    LIKE  TABLE OF bapimepoitem,   
      gs_poitem    LIKE  LINE  OF gt_poitem,
      gt_poitemx   LIKE  TABLE OF bapimepoitemx,
      gs_poitemx   LIKE  LINE  OF gt_poitemx.
CLEAR:gv_po_number,gs_poitemx,gs_poitem,gs_return,gt_poitemx,gt_poitem,gt_return.
"准备BAPI数据"
gv_po_number = po_number.
LOOP AT zpoitem INTO ls_zpoitem.
  gs_poitem-po_item    = ls_zpoitem-po_item.
  gs_poitem-delete_ind = ls_zpoitem-delete_ind.
  APPEND gs_poitem TO gt_poitem.
  gs_poitemx-po_item    = ls_zpoitem-po_item.
  gs_poitemx-po_itemx   = g_flag.
  gs_poitemx-delete_ind = g_flag.
  APPEND gs_poitemx TO gt_poitemx.
  CLEAR:gs_poitemx,gs_poitem.
ENDLOOP.
"执行BAPI BAPI_PO_CHANGE"
CALL FUNCTION 'BAPI_PO_CHANGE'
  EXPORTING
    purchaseorder  = gv_po_number
  TABLES
    return         = gt_return
    poitem         = gt_poitem
    poitemx        = gt_poitemx.
"判断执行结果，并记录错误信息"
READ TABLE gt_return INTO gs_return WITH KEY type = 'E'.
IF sy-subrc NE 0.
  flag = 'S'.
  CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
    EXPORTING
      wait = 'X'.
  CONCATENATE 'PO：'gv_po_number '处理完成' INTO message .
ELSE.
  CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
  LOOP AT gt_return INTO gs_return WHERE type CA 'AEX'.
    MESSAGE ID     gs_return-id
            TYPE   gs_return-type
            NUMBER gs_return-number
            WITH   gs_return-message_v1 gs_return-message_v2
                   gs_return-message_v3 gs_return-message_v4
            INTO lv_message.
    CONCATENATE message lv_message INTO message SEPARATED BY '｜'.
  ENDLOOP.
  SHIFT message LEFT DELETING LEADING  '｜'.
  flag = 'E'.
ENDIF.
ENDFUNCTION.
```

