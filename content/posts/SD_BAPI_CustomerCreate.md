---
title: " BAPI 创建 Customer "
date: 2019-10-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - business

tags: 
  - BAPI

---

### SD_CUSTOMER_MAINTAIN_ALL

TABLE 参数中有很多表，其中 X 开头代表要插入的数据，Y 开头代表要删除的数据。

```ABAP
DATA: TMP   TYPE STRING,
      LEN   TYPE I,
      FLAG  TYPE CHAR1,
      SY_SUBRC(2) TYPE C.
CLEAR:E_MESS.
*--判断是否存在同名客户
IF I_KNA1-KUNNR IS INITIAL.
  SELECT SINGLE NAME1 INTO TMP
    FROM KNA1
   WHERE NAME1 = I_KNA1-NAME1
     AND NAME2 = I_KNA1-NAME2.
  IF SY-SUBRC = 0.
    FLAG    = 'X'.
    E_STATU = 'E'.
    E_MESS  = '存在名称相同的客户'.
  ENDIF.
ENDIF.
*--判断邮编的长度
LEN = STRLEN( I_KNA1-PSTLZ ).
IF LEN <> 6.
  FLAG    = 'X'.
  E_STATU = 'E'.
  E_MESS  = '邮编应该是6位数'.
ENDIF.

IF FLAG = ''.
  CALL FUNCTION 'CONVERSION_EXIT_ALPHA_INPUT'
    EXPORTING
      INPUT   = I_KNA1-KUNNR
    IMPORTING
      OUTPUT  = I_KNA1-KUNNR.
  I_KNA1-LAND1 = 'CN'.
  I_KNA1-SPRAS = 1.
**--客户的销售数据
  I_KNVV-KUNNR = I_KNA1-KUNNR.
*  I_KNVV-VKORG =  ' ' .   "销售组织"
  I_KNVV-VTWEG =  '10' .   "分销渠道"
  I_KNVV-SPART =  '00' .   "产品组"
  I_KNVV-BZIRK =  ' ' .    "销售地区"
  I_KNVV-VKBUR =  ' ' .    "销售部门"
  I_KNVV-WAERS =  'RMB' .  "货币"
  I_KNVV-KALKS =  '1' .    "定价过程"
  I_KNVV-VERSG =  '1' .    "客户统计组"
  I_KNVV-ANTLF =  '9'.     "最大部分交货"
  I_KNVV-VSBED = '01'.     "装运条件"
  I_KNVV-KZAZU = 'X'.      "订单组合chk"
  I_KNVV-ZTERM = '9101'.   "付款条件"
  I_KNVV-KABSS = '0001'.   "付款担保过程"
  I_KNVV-KKBER = '9999'.   "信贷控制范围"
  I_KNVV-KTGRD = '01'.     "账户分配组"
  IF I_KNA1-KTOKD = 'A001'.
    I_KNVV-KDGRP =  '11'.  "客户组"
    I_KNVV-KONDA =  '01'.  "价格组"
    I_KNB1-FDGRV = 'E1'.   "现金管理组"
  ELSEIF I_KNA1-KTOKD = 'A002'.
    I_KNVV-KDGRP =  '21'.  "客户组"
    I_KNVV-KONDA =  '02'.  "价格组"
    I_KNB1-FDGRV = 'E2'.   "现金管理组"
  ENDIF.
**--客户的公司数据
  I_KNB1-KUNNR = I_KNA1-KUNNR.
  I_KNB1-BUKRS = I_KNVV-VKORG.
  I_KNB1-AKONT = ''.       "统驭科目"
  I_KNB1-ZTERM = '9101'.   "付款条件"
  I_KNB1-XZVER = 'X'.      "付款历史记录chk"
**--银行
  READ TABLE T_XKNBK INDEX 1.
  IF I_KNA1-KUNNR IS NOT INITIAL.
    SELECT SINGLE * INTO T_YKNBK
      FROM KNBK
     WHERE KUNNR = I_KNA1-KUNNR
       AND BANKS = 'CN'.
    IF SY-SUBRC EQ 0.
      APPEND T_YKNBK.
    ENDIF.
  ENDIF.
  T_XKNBK-KUNNR = I_KNA1-KUNNR.
  T_XKNBK-BANKS = 'CN'.         "银行国家代码"
  T_XKNBK-BANKL = '20000'.      "银行码"
  MODIFY T_XKNBK INDEX 1 TRANSPORTING KUNNR BANKS BANKL.
**--客户联系人
  READ TABLE T_XKNVK INDEX 1.
  IF I_KNA1-KUNNR IS NOT INITIAL.
    SELECT SINGLE * INTO T_YKNVK
      FROM KNVK
     WHERE KUNNR = I_KNA1-KUNNR.
    IF SY-SUBRC EQ 0.
      APPEND T_YKNVK.
    ENDIF.
  ENDIF.
  T_XKNVK-KUNNR = I_KNA1-KUNNR.
  T_XKNVK-NAMEV = '#'.
  T_XKNVK-ABTNR = '0002'.
  T_XKNVK-PAFKT = '02'.
  MODIFY T_XKNVK INDEX 1 TRANSPORTING KUNNR NAMEV ABTNR PAFKT.
**--合作伙伴
  IF I_KNA1-KUNNR IS NOT INITIAL.
    SELECT SINGLE * INTO T_YKNVP
      FROM KNVP
     WHERE KUNNR = I_KNA1-KUNNR
       AND VKORG = I_KNVV-VKORG
       AND VTWEG = '10'
       AND SPART = '00'
       AND PARVW = 'VE'.
    IF SY-SUBRC EQ 0.
      APPEND T_YKNVP.
    ENDIF.
  ENDIF.
  T_XKNVP-KUNNR = I_KNA1-KUNNR.
  T_XKNVP-VKORG =  I_KNVV-VKORG. "销售组织"
  T_XKNVP-VTWEG =  '10'.         "分销渠道"
  T_XKNVP-SPART =  '00'.         "产品组"
  T_XKNVP-PARVW = 'VE '.
*  T_XKNVP-PERNR = ''.
  MODIFY T_XKNVP INDEX 1 TRANSPORTING KUNNR  VKORG VTWEG SPART PARVW.
**--税收
   IF I_KNA1-KUNNR IS NOT INITIAL.
      SELECT SINGLE * INTO T_YKNVI
        FROM KNVI
       WHERE KUNNR = I_KNA1-KUNNR
         AND ALAND = 'CN'
         AND TATYP = 'MWST'.
      IF SY-SUBRC EQ 0.
        APPEND T_YKNVI.
      ENDIF.
    ENDIF.
    T_XKNVI-KUNNR = I_KNA1-KUNNR.
    T_XKNVI-ALAND = 'CN'.
    T_XKNVI-TATYP = 'MWST'.
    T_XKNVI-TAXKD = '1'.
    APPEND T_XKNVI .

  CALL FUNCTION 'SD_CUSTOMER_MAINTAIN_ALL'
   EXPORTING
     I_KNA1                              = I_KNA1
     I_KNB1                              = I_KNB1
     I_KNVV                              = I_KNVV
     I_MAINTAIN_ADDRESS_BY_KNA1          = 'X'
     I_KNB1_REFERENCE                    = I_KNB1_REFERENCE
     I_FORCE_EXTERNAL_NUMBER_RANGE       = I_FORCE_EXTERNAL_NUMBER_RANGE
     I_NO_BANK_MASTER_UPDATE             = I_NO_BANK_MASTER_UPDATE
     I_CUSTOMER_IS_CONSUMER              = I_CUSTOMER_IS_CONSUMER
     I_RAISE_NO_BTE                      = I_RAISE_NO_BTE
     PI_POSTFLAG                         = 'X'
     PI_CAM_CHANGED                      = PI_CAM_CHANGED
     PI_ADD_ON_DATA                      = PI_ADD_ON_DATA
     I_FROM_CUSTOMERMASTER               = 'X'
   IMPORTING
     E_KUNNR                             = E_KUNNR
     O_KNA1                              = O_KNA1
   TABLES
     T_XKNAS                             = T_XKNAS
     T_XKNBK                             = T_XKNBK
     T_XKNB5                             = T_XKNB5
     T_XKNEX                             = T_XKNEX
     T_XKNVA                             = T_XKNVA
     T_XKNVD                             = T_XKNVD
     T_XKNVI                             = T_XKNVI
     T_XKNVK                             = T_XKNVK
     T_XKNVL                             = T_XKNVL
     T_XKNVP                             = T_XKNVP
     T_XKNZA                             = T_XKNZA
     
     T_YKNAS                             = T_YKNAS
     T_YKNBK                             = T_YKNBK
     T_YKNB5                             = T_YKNB5
     T_YKNEX                             = T_YKNEX
     T_YKNVA                             = T_YKNVA
     T_YKNVD                             = T_YKNVD
     T_YKNVI                             = T_YKNVI
     T_YKNVK                             = T_YKNVK
     T_YKNVL                             = T_YKNVL
     T_YKNVP                             = T_YKNVP
     T_YKNZA                             = T_YKNZA
     T_UPD_TXT                           = T_UPD_TXT
   EXCEPTIONS
     CLIENT_ERROR                        = 1
     KNA1_INCOMPLETE                     = 2
     KNB1_INCOMPLETE                     = 3
     KNB5_INCOMPLETE                     = 4
     KNVV_INCOMPLETE                     = 5
     KUNNR_NOT_UNIQUE                    = 6
     SALES_AREA_NOT_UNIQUE               = 7
     SALES_AREA_NOT_VALID                = 8
     INSERT_UPDATE_CONFLICT              = 9
     NUMBER_ASSIGNMENT_ERROR             = 10
     NUMBER_NOT_IN_RANGE                 = 11
     NUMBER_RANGE_NOT_EXTERN             = 12
     NUMBER_RANGE_NOT_INTERN             = 13
     ACCOUNT_GROUP_NOT_VALID             = 14
     PARNR_INVALID                       = 15
     BANK_ADDRESS_INVALID                = 16
     TAX_DATA_NOT_VALID                  = 17
     NO_AUTHORITY                        = 18
     COMPANY_CODE_NOT_UNIQUE             = 19
     DUNNING_DATA_NOT_VALID              = 20
     KNB1_REFERENCE_INVALID              = 21
     CAM_ERROR                           = 22
     OTHERS                              = 23
            .
  IF SY-SUBRC NE 0.
    SY_SUBRC = SY-SUBRC.
    CALL FUNCTION 'BAPI_TRANSACTION_ROLLBACK'.
    E_STATU = 'E'.
    CONCATENATE '客户更新失败（' SY_SUBRC '）' INTO E_MESS.
  ELSE.
    CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
      EXPORTING
        WAIT          = 'X'.
    IF SY-SUBRC = 0.
      E_STATU = 'S'.
      E_MESS  = '客户更新成功'.
      E_KUNNR = O_KNA1-KUNNR.
    ELSE.
      E_STATU = 'E'.
      CONCATENATE '客户更新失败（' SY_SUBRC '）' INTO E_MESS.
    ENDIF.
  ENDIF.
ENDIF.
```

