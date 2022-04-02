---
title: " BAPI 操作BOM数据 "
date: 2021-07-18
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - MM
  - BAPI

---

BOM 内部 API：Function Group --- CSAI

### CSAI_BOM_XXX 和 CSAP_BOM_XX

CSAI_BOM_XXX ：This function module is used to create/change BOM item for sales. 

CSAP_DOC_BOM_MAINTAIN：用于处理简单的文档结构。主要用于改变结构，但也可用于创建结构。

如果您只想创建结构，请使用 CSAP_DOC_BOM_CREATE.

### 创建 BOM：CSAI_BOM_CREATE

```ABAP
DATA: LS_CSIN   TYPE CSIN,
  LS_STKOB  TYPE STKOB,
  LS_STZUB  TYPE STZUB,
  LS_STPOB  TYPE STPOB,
  LT_STPOB  TYPE TABLE OF STPOB,
  LT_STPOB2 TYPE TABLE OF STPO_API03.
DATA: LV_RESULT TYPE BAPI_MSGTY,
      LV_MESSAGE TYPE BAPI_MSG.
DATA: ZMAST TYPE MAST.
SELECT SINGLE * FROM MAST
  INTO CORRESPONDING FIELDS OF ZMAST
 WHERE MATNR = MATNR
   AND WERKS = WERKS
   AND STLAN = STLAN
   AND STLAL = STLAL
IF SY-SUBRC EQ 0.
  LV_RESULT = 'E'.
  CONCATENATE MATNR 'BOM already exists in SAP'
     INTO LV_MESSAGE SEPARATED BY SPACE.
  CONTINUE.
ENDIF.
"Interface for BOM Access"
LS_CSIN-MATNR = MATNR. "物料编号"
LS_CSIN-WERKS = WERKS. "工厂"
LS_CSIN-STLAN = STLAN. "BOM用途"
LS_CSIN-STLAL = STLAL. "替代物料清单"
LS_CSIN-DATUV = DATUV. "有效起始日期"
LS_CSIN-AENNR = AENNR. "更改编号"
LS_CSIN-STLTY = 'M'.   "BOM category:M(Material BOM)"
"BOM Header Document Table"
LS_STKOB-STLTY = 'M'.
LS_STKOB-BMENG = BMENG. "基本数量"
LS_STKOB-STLST = '01'.  "BOM状态:01(Active)"
LS_STKOB-STKTX = '' .   "BOM文本"
"行项目数据"
LS_STPOB-STLTY = 'M'.
LS_STPOB-SANFE = 'X'.
LS_STPOB-POSNR = 23.
LS_STPOB-POSTP = POSTP. "项目类别"
LS_STPOB-IDNRK = IDNRK.
LS_STPOB-MENGE = MENGE * BMENG. "数量=原数量*放大倍数"
LS_STPOB-MEINS = MEINS . "单位"
LS_STPOB-NFGRP = NFGRP . "Follow-up group"
LS_STPOB-NFEAG = NFEAG . "Discontinuation group"
LS_STPOB-ALPST = ALPST . "Alternative item: strategy"
LS_STPOB-DSPST = DSPST . "Explosion type"
LS_STPOB-ALPGR = ALPGR . "Alternative item: group"
LS_STPOB-ALPRF = ALPRF . "Alternative item: ranking order"
LS_STPOB-SANKA = SANKA . "Indicator: item relevant to costing"
LS_STPOB-EWAHR = EWAHR . "Usage probability in % (alternative item)"
APPEND LS_STPOB TO LT_STPOB.
CLEAR LS_STPOB.
"调用 BAPI"
CALL FUNCTION 'CSAI_BOM_CREATE'
  EXPORTING
    ECSIN              = LS_CSIN
    ESTKOB             = LS_STKOB
    ESTZUB             = LS_STZUB
    FL_COMMIT_AND_WAIT = 'X'
  IMPORTING
    FL_WARNING         = L_WARNING
    ASTLNR             = L_ASTLNR
  TABLES
    T_STPOB            = LT_STPOB
  EXCEPTIONS
    ERROR              = 1
    OTHERS             = 2.
```

### 修改 BOM：CSAI_BOM_MAINTAIN

```ABAP
  DATA: LS_STKO LIKE STKO_API01.
  DATA: L_POS     TYPE STPOB-POSNR,
        LS_STPOB  LIKE STPO_API03,
        LT_STPOB  TYPE TABLE OF STPO_API03,
CALL FUNCTION 'CSAP_MAT_BOM_MAINTAIN'
  EXPORTING
    MATERIAL           = LS_HEAD-MATERIAL
    PLANT              = LS_HEAD-PLANT
    BOM_USAGE          = LS_HEAD-BOM_USAGE
    VALID_FROM         = LS_HEAD-VALID_FROM
    CHANGE_NO          = LS_HEAD-CHANGE_NO
    I_STKO             = LS_STKO
    FL_COMMIT_AND_WAIT = 'X'
*   FL_NEW_ITEM        = 'X'
*   FL_BOM_CREATE      = 'X'
  IMPORTING
    FL_WARNING         = L_WARNING
*   O_STKO             = LT_O_STKO
  TABLES
    T_STPO             = LT_STPOB[]
  EXCEPTIONS
    ERROR              = 1
    OTHERS             = 2.

    CALL FUNCTION 'BALW_BAPIRETURN_GET2'
      EXPORTING
        TYPE   = SY-MSGTY
        CL     = SY-MSGID
        NUMBER = SY-MSGNO
        PAR1   = SY-MSGV1
        PAR2   = SY-MSGV2
        PAR3   = SY-MSGV3
        PAR4   = SY-MSGV4
*       PARAMETER  = P_PARAMETER
*       ROW    = P_ROW
*       FIELD  = ' '
      IMPORTING
        RETURN = LT_RETURN.
    APPEND LT_RETURN.
```

### 读取 BOM：CSAI_BOM_READ

```ABAP
DATA: gs_csin TYPE csin,
      gs_stko TYPE stkob,
      gs_stzu TYPE stzub.
DATA: gt_stpo TYPE TABLE OF stpob,
      gt_stko TYPE TABLE OF stkob,
      gs_stpo TYPE stpob.

CALL FUNCTION 'CSAI_BOM_READ'
  EXPORTING
    ecsin = gs_csin
* IMPORTING
* FL_WARNING =
  TABLES
    t_stpob = gt_stpo
    t_stkob = gt_stko
* T_DEP_DATA =
* T_DEP_DESCR =
* T_DEP_ORDER =
* T_DEP_SOURCE =
* T_DEP_DOC =
* EXCEPTIONS
* ERROR = 1
* OTHERS = 2
.
IF sy-subrc <> 0.
* MESSAGE ID SY-MSGID TYPE SY-MSGTY NUMBER SY-MSGNO
* WITH SY-MSGV1 SY-MSGV2 SY-MSGV3 SY-MSGV4.
ENDIF.
```

### 删除 BOM：CSAI_BOM_DELETE