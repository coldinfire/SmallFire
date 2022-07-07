---
title: " STVARV 使用详情 "
date: 2019-02-27
draft: false
author: Small Fire
isCJKLanguage: true
categories: 
  - ABAP

tags: 
  - saputils

---

### STVARV 使用

该配置不能跨 Client 因此需要在每个 Client 单独配置；使用事物码`STVARV`进入配置界面，可以新建、修改、删除记录。

![Maintain](/images/ABAP/Stvarv.png)

### 程序内使用

程序内只是取出配置的值，该值存储在表`TVARVC`中。如果有前导零问题，需要调用 Function 添加前导零。

```ABAP
DATA: LT_TVARVC TYPE TABLE OF TVARVC,
      LS_TVARVC TYPE TVARVC.
"创建Ranges"
DATA: LT_TABLE TYPE RANGE OF field,
      LS_TABLE LIKE LINE OF LT_TABLE,
      LS_VALUE TYPE CHAR10.
"Parameter类型数据"
SELECT SINGLE * INTO CORRESPONDING FIELDS OF LS_TVARVC
  FROM TVARVC
 WHERE NAME = 'Parameter_key'
   AND TYPE = 'P'.
IF SY-SUBRC = 0 .
  LS_VALUE = LS_TVARVC-LOW.
ENDIF.
"Selection Options类型数据"
SELECT * INTO CORRESPONDING FIELDS OF TABLE LT_TVARVC
  FROM TVARVC
 WHERE NAME = 'Selection_key'
   AND TYPE = 'S'.
IF SY-SUBRC = 0.
  LOOP AT LT_TVARVC INTO LS_TVARVC.
    LS_TABLE-SIGN = LS_TVARVC-SIGN.
    LS_TABLE-OPTION = LS_TVARVC-OPTI.
  	LS_TABLE-LOW  = LS_TVARVC-LOW.
    LS_TABLE-HIGH = LS_TVARVC-HIGH.
    APPEND LS_TABLE TO LT_TABLE.
  ENDLOOP.
ENDIF.
```

