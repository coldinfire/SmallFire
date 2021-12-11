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



### 读取 BOM：CSAI_BOM_READ



### 删除 BOM：CSAI_BOM_DELETE